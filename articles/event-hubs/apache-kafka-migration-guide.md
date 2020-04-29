---
title: Migrar para os hubs de eventos do Azure para Apache Kafka
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677362"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrar para os hubs de eventos do Azure para Apache Kafka ecossistemas
Os hubs de eventos do Azure expõem um ponto de extremidade Apache Kafka, que permite que você se conecte aos hubs de eventos usando o protocolo Kafka. Fazendo alterações mínimas em seu aplicativo Kafka existente, você pode se conectar aos hubs de eventos do Azure e colher os benefícios do ecossistema do Azure. Hubs de eventos para suporte do Kafka [Apache Kafka versão 1,0](https://kafka.apache.org/10/documentation.html) e posterior.

## <a name="pre-migration"></a>Pré-migração 

### <a name="create-an-azure-account"></a>Criar uma conta do Azure
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

### <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
Siga as instruções passo a passo no artigo [criar um hub de eventos](event-hubs-create.md) para criar um namespace de hubs de eventos e um hub de eventos. 

### <a name="connection-string"></a>Cadeia de conexão
Siga as etapas do artigo [obter a cadeia de conexão do portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . E anote a cadeia de conexão para uso posterior. 

### <a name="fully-qualified-domain-name-fqdn"></a>FQDN (nome de domínio totalmente qualificado)
Você também pode precisar do FQDN que aponta para o namespace do hub de eventos. O FQDN pode ser encontrado na cadeia de conexão da seguinte maneira:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se o seu namespace de hubs de eventos for implantado em uma nuvem não pública, seu nome de domínio poderá \*ser diferente ( \*por exemplo,. \*servicebus.chinacloudapi.cn,. ServiceBus.usgovcloudapi.net ou. ServiceBus.cloudapi.de).

## <a name="migration"></a>Migração 

### <a name="update-your-kafka-client-configuration"></a>Atualizar a configuração do cliente Kafka

Para se conectar a um hub de eventos habilitado para Kafka, você precisará atualizar as configurações de cliente do Kafka. Se você estiver tendo problemas para encontrar o seu, tente pesquisar `bootstrap.servers` onde está definido em seu aplicativo.

Insira as configurações a seguir onde quer que faça sentido em seu aplicativo. Certifique-se de atualizar `bootstrap.servers` os `sasl.jaas.config` valores e para direcionar o cliente para o ponto de extremidade Kafka dos hubs de eventos com a autenticação correta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` não for uma configuração com suporte em sua estrutura, localize as configurações usadas para definir o nome de usuário e a senha SASL e use-as. Defina o nome de `$ConnectionString` usuário como e a senha para sua cadeia de conexão de hubs de eventos.

## <a name="post-migration"></a>Pós-migração
Execute o aplicativo Kafka que envia eventos para o Hub de eventos. Em seguida, verifique se o Hub de eventos recebe os eventos usando o portal do Azure. Na página **visão geral** do namespace de seus hubs de eventos, alterne para o modo de exibição **mensagens** na seção **métricas** . Atualize a página para atualizar o gráfico. Poderá levar alguns segundos para que ela mostre que as mensagens foram recebidas. 

[![Verificar se o hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia de solução de problemas Apache Kafka para hubs de eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes-hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)