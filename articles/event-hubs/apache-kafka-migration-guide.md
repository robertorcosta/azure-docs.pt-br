---
title: Migre para os Hubs de Eventos do Azure para Apache Kafka
description: Este artigo mostra como consumidores e produtores que usam protocolos diferentes (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao usar os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677362"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migre para hubs de eventos do Azure para ecossistemas Apache Kafka
O Azure Event Hubs expõe um ponto final apache kafka, que permite que você se conecte aos Hubs de Eventos usando o protocolo Kafka. Ao fazer alterações mínimas no aplicativo Kafka existente, você pode se conectar ao Azure Event Hubs e colher os benefícios do ecossistema Azure. Hubs de eventos para Kafka suportam [Apache Kafka versão 1.0](https://kafka.apache.org/10/documentation.html) e posterior.

## <a name="pre-migration"></a>Pré-migração 

### <a name="create-an-azure-account"></a>Criar uma conta do Azure
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

### <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
Siga as instruções passo a passo no Criar um artigo [do hub de eventos](event-hubs-create.md) para criar um namespace do Event Hubs e um hub de eventos. 

### <a name="connection-string"></a>Cadeia de conexão
Siga os passos da [seqüência de conexão Obter a partir do](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) artigo do portal. E, anote a seqüência de conexão para uso posterior. 

### <a name="fully-qualified-domain-name-fqdn"></a>Nome de domínio totalmente qualificado (FQDN)
Você também pode precisar do FQDN que aponta para o seu namespace do Event Hub. O FQDN pode ser encontrado dentro da seqüência de conexões da seguinte forma:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se o namespace do Event Hubs for implantado em uma nuvem não \*pública, \*seu nome \*de domínio poderá diferir (por exemplo, .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net ou .servicebus.cloudapi.de).

## <a name="migration"></a>Migração 

### <a name="update-your-kafka-client-configuration"></a>Atualize sua configuração de cliente Kafka

Para se conectar a um Hub de eventos habilitado para Kafka, você precisará atualizar as configurações do cliente Kafka. Se você está tendo problemas para encontrar `bootstrap.servers` o seu, tente procurar onde está definido em sua aplicação.

Insira as seguintes configurações onde quer que faça sentido em sua aplicação. Certifique-se de `bootstrap.servers` `sasl.jaas.config` atualizar os valores e direcionar o cliente para o ponto final do Event Hubs Kafka com a autenticação correta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` não for uma configuração suportada em sua estrutura, encontre as configurações usadas para definir o nome de usuário e senha do SASL e use-as em seu lugar. Defina o `$ConnectionString` nome de usuário e a senha da seqüência de conexões do Event Hubs.

## <a name="post-migration"></a>Após a migração
Execute seu aplicativo Kafka que envia eventos para o centro de eventos. Em seguida, verifique se o hub de eventos recebe os eventos usando o portal Azure. Na **página Visão geral** do namespace do event Hubs, mude para a exibição **mensagens** na seção **Métricas.** Atualize a página para atualizar o gráfico. Poderá levar alguns segundos para que ela mostre que as mensagens foram recebidas. 

[![Verificar se o hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia de solução de problemas apache Kafka para Hubs de Eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de desenvolvedores Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)