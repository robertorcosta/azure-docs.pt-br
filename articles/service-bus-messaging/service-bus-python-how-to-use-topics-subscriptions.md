---
title: 'Início Rápido: Usar tópicos e assinaturas do Barramento de Serviço do Azure com Python'
description: Este artigo mostra como criar um tópico e uma assinatura do Barramento de Serviço do Azure e enviar mensagens a um tópico e receber mensagens da assinatura.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 03e22c4c179850e5140015c0abc2d89f16d4b624
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774553"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Início Rápido: Usar tópicos e assinaturas do Barramento de Serviço com Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar o Python com tópicos e assinaturas do Barramento de Serviço do Azure. Os exemplos usam o pacote [SDK do Python do Azure][Azure Python package] para: 

- Criar tópicos e assinaturas para tópicos
- Criar filtros e regras de assinatura
- Enviar mensagens para tópicos 
- Receber mensagens de assinaturas
- Excluir tópicos e assinaturas

## <a name="prerequisites"></a>Prerequisites
- Uma assinatura do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um namespace do Barramento de Serviço, criado seguindo as etapas em [Início Rápido: Usar o portal do Azure para criar assinaturas e um tópico do Barramento de Serviço](service-bus-quickstart-topics-subscriptions-portal.md). Copie o nome do namespace, o nome da chave de acesso compartilhada e o valor da chave primária da tela **Políticas de acesso compartilhado** para usar posteriormente nesse guia de início rápido. 
- Python 3.4 x ou superior, com o pacote [SDK do Python do Azure][Azure Python package] instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Criar um objeto ServiceBusService

Um objeto **ServiceBusService** permite que você trabalhe com tópicos e assinaturas para tópicos. Para acessar programaticamente o Barramento de Serviço, adicione a seguinte linha perto da parte superior do arquivo do Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adicione o código a seguir para criar um objeto **ServiceBusService**. Substitua `<namespace>`, `<sharedaccesskeyname>` e `<sharedaccesskeyvalue>` pelo nome do namespace do Barramento de Serviço, o nome da chave de SAS (Assinatura de Acesso Compartilhado) e o valor da chave primária. Você pode encontrar esses valores em **Políticas de acesso compartilhado** no namespace do Barramento de Serviço no [portal do Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Criar um tópico

O código a seguir usa o método `create_topic` para criar um tópico do Barramento de Serviço chamado `mytopic`, com configurações padrão:

```python
bus_service.create_topic('mytopic')
```

Você pode usar as opções de tópico para substituir as configurações de tópico padrão, como TTL (vida útil) da mensagem ou tamanho máximo do tópico. O exemplo a seguir cria um tópico chamado `mytopic` com um tamanho máximo de tópico de 5 GB e TTL da mensagem padrão de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

Você também usa o objeto **ServiceBusService** para criar assinaturas para tópicos. Uma assinatura pode ter um filtro para restringir o conjunto de mensagens entregue à sua fila virtual. Se você não especificar um filtro, as novas assinaturas usarão o filtro padrão **MatchAll**, que coloca todas as mensagens publicadas no tópico na fila virtual da assinatura. O exemplo a seguir cria uma assinatura para `mytopic` chamada `AllMessages` que usa o filtro **MatchAll**:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Usar filtros com assinaturas

Use o método `create_rule` do objeto **ServiceBusService** para filtrar as mensagens que aparecem em uma assinatura. Você pode especificar regras ao criar a assinatura ou adicionar regras às assinaturas existentes.

O tipo de filtro mais flexível é um **SqlFilter**, que usa um subconjunto de SQL-92. Os filtros SQL operam com base nas propriedades das mensagens publicadas no tópico. Para obter mais informações sobre as expressões que podem ser usadas com um filtro SQL, confira a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Como o filtro padrão **MatchAll** se aplica automaticamente a todas as novas assinaturas, você deve removê-lo das assinaturas que deseja filtrar, senão o filtro **MatchAll** substituirá todos os outros filtros especificados. Você pode remover a regra padrão usando o método `delete_rule` do objeto **ServiceBusService**.

O exemplo a seguir cria uma assinatura para `mytopic` chamada `HighMessages`, com uma regra **SqlFilter** chamada `HighMessageFilter`. A regra `HighMessageFilter` seleciona apenas as mensagens com uma propriedade `messageposition` personalizada maior que 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

O exemplo a seguir cria uma assinatura para `mytopic` chamada `LowMessages`, com uma regra **SqlFilter** chamada `LowMessageFilter`. A regra `LowMessageFilter` seleciona apenas as mensagens com uma propriedade `messageposition` menor ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Com `AllMessages`, `HighMessages` e `LowMessages` em vigor, as mensagens enviadas para `mytopic` são sempre entregues aos destinatários da assinatura `AllMessages`. As mensagens também são fornecidas seletivamente para a assinatura `HighMessages` ou `LowMessages`, dependendo do valor da propriedade `messageposition` da mensagem. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Os aplicativos usam o método `send_topic_message` do objeto **ServiceBusService** para enviar mensagens para um tópico do Barramento de Serviço.

O exemplo a seguir envia cinco mensagens de teste para o tópico `mytopic`. O valor da propriedade `messageposition` personalizada depende da iteração do loop e determina quais assinaturas recebem as mensagens. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Cotas e limites de tamanho de mensagem

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite no número de mensagens que um tópico pode conter, mas há um limite no tamanho total das mensagens que o tópico contém. Você pode definir o tamanho do tópico no momento da criação, com um limite superior de 5 GB. 

Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

Os aplicativos usam o método `receive_subscription_message` no objeto **ServiceBusService** para receber mensagens de uma assinatura. O exemplo a seguir recebe mensagens da assinatura `LowMessages` e as exclui conforme elas são lidas:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

O parâmetro opcional `peek_lock` de `receive_subscription_message` determina se o Barramento de Serviço exclui as mensagens da assinatura conforme elas são lidas. O modo padrão para recebimento de mensagens é *PeekLock* ou `peek_lock` definido como **True**, que lê (espia) e bloqueia mensagens sem excluí-las da assinatura. Cada mensagem deve ser explicitamente concluída para removê-la da assinatura.

Para excluir mensagens da assinatura conforme elas são lidas, você pode definir o parâmetro `peek_lock` como **False**, como no exemplo anterior. A exclusão de mensagens como parte da operação de recebimento é o modelo mais simples e funcionará bem se o aplicativo puder tolerar mensagens ausentes caso haja uma falha. Para entender esse comportamento, considere um cenário no qual o aplicativo emite uma solicitação de recebimento e, em seguida, falha antes de processá-la. Se a mensagem foi excluída ao ser recebida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem recebida antes da falha.

Se seu aplicativo não puder tolerar mensagens perdidas, o recebimento se tornará uma operação de duas fases. O PeekLock localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e a retorna para o aplicativo. Após o processamento ou o armazenamento da mensagem, o aplicativo conclui a segunda fase do processo de recebimento chamando o método `complete` no objeto **Message**.  O método `complete` marcará a mensagem como sendo consumida e a removerá da assinatura.

O exemplo a seguir demonstra um cenário de PeekLock:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não conseguir processar a mensagem por algum motivo, ele chamará o método `unlock` no objeto **Message**. O Barramento de Serviço desbloqueia a mensagem na assinatura e a disponibiliza para ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite para mensagens bloqueadas na assinatura. Se um aplicativo não conseguir processar uma mensagem antes que o tempo limite de bloqueio expire (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `complete` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele for reiniciado. Esse comportamento geralmente é chamado de *Processamento pelo menos uma vez*. Cada mensagem é processada pelo menos uma vez, mas em determinadas situações, a mesma mensagem poderá ser reenviada. Se o seu cenário não puder tolerar o processamento duplicado, você poderá usar a propriedade **MessageId**, que permanece constante entre as tentativas de entrega, para lidar com a entrega de mensagens duplicada. 

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas

Para excluir tópicos e assinaturas, use o [portal do Azure][Azure portal] ou o método `delete_topic`. O código a seguir exclui o tópico chamado `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A exclusão de um tópico exclui todas as assinaturas para o tópico. Você também pode excluir as assinaturas de forma independente. O código a seguir exclui a assinatura chamada `HighMessages` do tópico `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Por padrão, os tópicos e as assinaturas são persistentes e existem até você excluí-los. Para excluir assinaturas automaticamente após um determinado período decorrido, você pode definir o parâmetro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) na assinatura. 

> [!TIP]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que você se conecte a um namespace do Barramento de Serviço e administre facilmente as entidades de mensagens. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais:

* [Filas, tópicos e assinaturas][Queues, topics, and subscriptions]
* Referência de [SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
