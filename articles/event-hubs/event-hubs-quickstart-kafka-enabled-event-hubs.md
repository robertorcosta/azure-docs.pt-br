---
title: 'Início Rápido: streaming de dados com os Hubs de Eventos do Azure usando o protocolo Kafka'
description: 'Início Rápido: Este artigo fornece informações sobre como fazer uma transmissão nos Hubs de Eventos do Azure usando o protocolo Kafka e APIs.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 18976a29a716a0e5a627747d98edc0d3e1bf71e9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587134"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Início Rápido: Fluxo de dados com os Hubs de Eventos usando o protocolo Kafka
Este guia de início rápido mostra como transmitir para o Kafka habilitado dos Hubs de Eventos sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Você aprenderá a usar seu produtores e os consumidores para se comunicar com os Hubs de Eventos habilitados com Kafka com apenas uma alteração de configuração nos seus aplicativos. Hubs de Eventos do Azure dá suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa atender aos seguinte pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Realizar o download](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um armazenamento binário Maven
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um Kafka habilitado com o namespace dos Hubs de Evento
Quando você cria um namespace dos Hubs de Eventos do nível **Standard**, o ponto de extremidade do Kafka para o namespace é habilitado automaticamente. Você pode transmitir eventos dos seus aplicativos que usam o protocolo Kafka para Hubs de Eventos do nível Standard. Siga as instruções passo a passo em [Criar um hub de eventos usando o portal do Azure](event-hubs-create.md) para criar um namespace de Hubs de Eventos do nível **Standard**. 

> [!NOTE]
> Os Hubs de Eventos para Kafka estão disponíveis somente nas camadas **Standard** e **dedicadas**. O nível **básico** não dá suporte a Kafka em Hubs de Eventos.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Enviar e receber mensagens com Kafka no Hubs de Evento

1. Clone o [repositório de Hubs de Eventos do Azure para Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navegue até `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Atualize os detalhes de configuração para o produtor no `src/main/resources/producer.config` da seguinte maneira:

    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Encontre o código-fonte da classe de manipulador de exemplo CustomAuthenticateCallbackHandler no GitHub [aqui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Execute o código do produtor e transmita eventos para os Hubs de Eventos habilitados para o Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navegue até `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Atualize os detalhes de configuração para o consumidor no `src/main/resources/consumer.config` da seguinte maneira:
   
    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Encontre o código-fonte da classe de manipulador de exemplo CustomAuthenticateCallbackHandler no GitHub [aqui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Encontre todas as amostras do OAuth para os Hubs de Eventos para o Kafka [aqui](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Execute o código do consumidor e processo a partir do Hubs de Eventos habilitados com Kafka usando seus clientes Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se seu cluster Kafka de Hubs de Eventos tiver eventos, inicie agora recebendo-os do consumidor.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como transmitir para os Hubs de Eventos com Kafka habilitado sem alterar seus clientes de protocolo ou seus próprios clusters em execução. Para saber mais, confira os seguintes artigos e as seguintes amostras:

- [Saiba mais sobre os Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Inícios rápidos dos Hubs de Eventos para o Kafka no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Tutoriais dos Hubs de Eventos para o Kafka no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para Hubs de Eventos habilitados para Kafka na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
- Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [Apache Flink](event-hubs-kafka-flink-tutorial.md) ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
