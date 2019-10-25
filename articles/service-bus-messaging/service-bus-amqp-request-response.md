---
title: Operações de solicitação/resposta do AMQP 1,0 no barramento de serviço do Azure
description: Lista de operações baseadas em solicitação-resposta do Barramento de Serviço do Microsoft Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: b549aee197f35df29a982a1a86644c46b9061c63
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785268"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 no Barramento de Serviço do Microsoft Azure: operações baseadas em solicitação-resposta

Este artigo define a lista de operações baseadas em solicitação/resposta do Barramento de Serviço do Microsoft Azure. Estas informações baseiam-se no rascunho funcional do Gerenciamento de AMQP Versão 1.0.  
  
Para um guia de protocolo AMQP 1.0 detalhado no nível de transmissão, que explica como o Barramento de Serviço implementa e se baseia na especificação técnica AMQP OASIS, confira o [AMQP 1.0 no guia de protocolo do Barramento de Serviço do Azure e dos Hubs de Eventos][guia do protocolo amqp 1.0].  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição de entidade  

Uma descrição de entidade refere-se a um objeto da [classe QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) ou [classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) do Barramento de Serviço.  
  
### <a name="brokered-message"></a>Mensagem agenciada  

Representa uma mensagem no Barramento de Serviço, que é mapeada para uma mensagem AMQP. O mapeamento é definido no [guia de protocolo do AMQP no Barramento de Serviço](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Anexar ao nó de gerenciamento de entidades  

Todas as operações descritas neste documento seguem um padrão de solicitação/resposta, estão dentro do escopo de uma entidade e exigem a anexação a um nó de gerenciamento de entidades.  
  
### <a name="create-link-for-sending-requests"></a>Criar link para enviar solicitações  

Cria um link para o envio de solicitações do nó de gerenciamento.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Criar link para receber respostas  

Cria um link para receber as respostas do nó de gerenciamento.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Transferir uma mensagem de solicitação  

Transfere uma mensagem de solicitação.  
Um estado de transação pode ser adicionado, opcionalmente, para operações que dão suporte a transações.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Receber uma mensagem de resposta  

Recebe a mensagem de resposta do link de resposta.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A mensagem de resposta está no seguinte formato:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Endereço de entidade do Barramento de Serviço  

As entidades do Barramento de Serviço devem ser abordadas da seguinte maneira:  
  
|Tipo de entidade|Endereço|Exemplo|  
|-----------------|-------------|-------------|  
|fila|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagem  
  
### <a name="message-renew-lock"></a>Renovar Bloqueio da Mensagem  

Estende o bloqueio de uma mensagem até o momento especificado na descrição da entidade.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
 O corpo da mensagem de solicitação deve consistir em uma seção amqp-value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de uuid|SIM|Tokens de bloqueio de mensagem a serem renovados.|  

> [!NOTE]
> Lock tokens são a propriedade `DeliveryTag` nas mensagens recebidas. Consulte o exemplo a seguir no [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. O token também pode aparecer no 'DeliveryAnnotations' como 'x-opt-lock-token', no entanto, isso não é garantido e `DeliveryTag` deve ser o preferencial. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção amqp-value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expirations|matriz de carimbo de data/hora|SIM|Nova expiração do token de bloqueio de mensagem correspondente aos tokens de bloqueio da solicitação.|  
  
### <a name="peek-message"></a>Espiar mensagem  

Espia mensagens sem bloqueio.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|longo|SIM|Número de sequência do qual a espiada será iniciada.|  
|`message-count`|int|SIM|Número máximo de mensagens a serem espiadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|SIM|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|Message|matriz de bytes|SIM|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens. Esta operação oferece suporte a transações.
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|SIM|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message-id|string|SIM|`amqpMessage.Properties.MessageId` como uma cadeia de caracteres|  
|session-id|string|Não|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|Não|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|chave por meio de partição|string|Não|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|Message|matriz de bytes|SIM|Mensagem codificada por transmissão AMQP 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|SIM|Número de sequência das mensagens agendadas. O número de sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens agendadas.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|SIM|Números de sequência das mensagens agendadas a serem canceladas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|   
  
## <a name="session-operations"></a>Operações da sessão  
  
### <a name="session-renew-lock"></a>Renovar Bloqueio da Sessão  

Estende o bloqueio de uma mensagem até o momento especificado na descrição da entidade.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SIM|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expiração|timestamp|SIM|Nova expiração.|  
  
### <a name="peek-session-message"></a>Espirar Mensagem da Sessão  

Espia as mensagens da sessão sem bloqueio.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|longo|SIM|Número de sequência do qual a espiada será iniciada.|  
|message-count|int|SIM|Número máximo de mensagens a serem espiadas.|  
|session-id|string|SIM|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|SIM|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|Message|matriz de bytes|SIM|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="set-session-state"></a>Definir Estado de Sessão  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SIM|ID da sessão.|  
|session-state|matriz de bytes|SIM|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
### <a name="get-session-state"></a>Obter Estado de Sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|SIM|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-state|matriz de bytes|SIM|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Enumerar Sessões  

Enumera as sessões em uma entidade de mensagens.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|SIM|Um filtro para incluir apenas as sessões atualizadas após determinado tempo.|  
|skip|int|SIM|Ignore um número de sessões.|  
|top|int|SIM|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|skip|int|SIM|Número de sessões ignoradas se o código de status for 200.|  
|sessions-ids|matriz de cadeias de caracteres|SIM|Matriz de IDs de sessão se o código de status for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|SIM|Nome da regra, não incluindo nomes de tópico e assinatura.|  
|rule-description|map|SIM|Descrição da regra, conforme especificado na próxima seção.|  
  
O mapa **rule-description** deve incluir as seguintes entradas, em que **sql-filter** e **correlation-filter** são mutuamente exclusivos:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|SIM|`sql-filter`, conforme especificado na próxima seção.|  
|correlation-filter|map|SIM|`correlation-filter`, conforme especificado na próxima seção.|  
|sql-rule-action|map|SIM|`sql-rule-action`, conforme especificado na próxima seção.|  
  
O mapa sql-filter deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|SIM|Expressão de filtro SQL.|  
  
O mapa **correlation-filter** deve incluir, pelo menos, uma das seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|Não||  
|message-id|string|Não||  
|para|string|Não||  
|reply-to|string|Não||  
|label|string|Não||  
|session-id|string|Não||  
|reply-to-session-id|string|Não||  
|content-type|string|Não||  
|properties|map|Não|É mapeado para [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) do Barramento de Serviço.|  
  
O mapa **sql-rule-action** deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|SIM|Expressão de ação do SQL.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
### <a name="remove-rule"></a>Remover Regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|SIM|Nome da regra, não incluindo nomes de tópico e assinatura.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
### <a name="get-rules"></a>Obter regras

#### <a name="request"></a>Solicitação

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:

|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  

O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|top|int|SIM|O número de regras para buscar na página.|  
|skip|int|SIM|O número de regras a serem ignoradas. Define o índice inicial (+ 1) na lista de regras. | 

#### <a name="response"></a>Resposta

O mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|regras| matriz de mapa|SIM|Matriz de regras. Cada regra é representada por um mapa.|

Cada entrada de mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-description|matriz de objetos descritos|SIM|`com.microsoft:rule-description:list` com AMQP descrito código 0x0000013700000004| 

`com.microsoft.rule-description:list` é uma matriz de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | matriz de objetos descritos | SIM | `filter` conforme especificado abaixo. |
| 1 | matriz de objeto descrito | SIM | `ruleAction` conforme especificado abaixo. |
| 2 | string | SIM | nome da regra. |

`filter` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Value |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro True que representa 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro False que representa 1=0 |

`com.microsoft:sql-filter:list` é uma matriz descrita que inclui:

|Índice|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | string | SIM | Expressão de filtro SQL |

`com.microsoft:correlation-filter:list` é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de valor|Conteúdo de valor|  
|---------|----------------|--------------|
| 0 | string | ID de Correlação |
| 1 | string | ID da Mensagem |
| 2 | string | Para |
| 3 | string | Responder Para |
| 4 | string | Rótulo |
| 5 | string | ID da sessão |
| 6 | string | ID da Sessão Responder Para|
| 7 | string | Tipo de conteúdo |
| 8 | Mapa | Mapa de propriedades de aplicativo definido |

`ruleAction` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Value |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Ação de regra vazia - nenhuma ação de regra presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Ação de regra SQL |

`com.microsoft:sql-rule-action:list` é uma matriz de objetos descritos cuja primeira entrada é uma cadeia de caracteres que contém a expressão da ação de regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagem adiada  
  
### <a name="receive-by-sequence-number"></a>Receber pelo número de sequência  

Recebe as mensagens adiadas por número de sequência.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|SIM|Números de sequência.|  
|receiver-settle-mode|ubyte|SIM|Modo de **liquidação do receptor**, conforme especificado no AMQP Core v1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|SIM|Lista de mensagens, em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|SIM|Token de bloqueio se `receiver-settle-mode` for 1.|  
|Message|matriz de bytes|SIM|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Atualizar status de disposição  

Atualiza o status de disposição das mensagens adiadas. Esta operação oferece suporte a transações.
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|SIM|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|SIM|concluído<br /><br /> abandonado<br /><br /> suspenso|  
|lock-tokens|matriz de uuid|SIM|Tokens de bloqueio de mensagem para atualizar o status de disposição.|  
|deadletter-reason|string|Não|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|deadletter-description|string|Não|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|properties-to-modify|map|Não|Lista de propriedades de mensagem agenciada do Barramento de Serviço a serem modificadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|obrigatórios|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|SIM|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
