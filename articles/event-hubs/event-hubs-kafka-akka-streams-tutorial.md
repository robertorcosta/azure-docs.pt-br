---
title: Usando o Akka Streams para o Apache Kafka – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como conectar o Akka Streams a um hub de eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 0b96f1448fd223aae2dde77c5c05a8c9bd74ee9b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632850"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Usar o Akka Streams com Hubs de Eventos para o Apache Kafka
Este tutorial mostra como conectar o Akka Streams a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. Hubs de Eventos do Azure para Kafka dá suporte para o [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar produtor do Akka Streams 
> * Executar consumidor do Akka Streams

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário atender aos seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [JDK (Java Development Kit) 1.8+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixe](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace dos Hubs de Eventos para enviar ou receber de qualquer serviço de Hubs de Eventos. Consulte [Criar um hub de eventos](event-hubs-create.md) para obter informações detalhadas. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma seqüência de conexões Event Hubs, clone os Hubs de eventos do Azure para o repositório Kafka e navegue até a `akka` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Executar produtor do Akka Streams

Usando o exemplo do produtor do Akka Streams, envie mensagens para o serviço de Hubs de Eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="producer-applicationconf"></a>Produtor application.conf

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `producer/src/main/resources/application.conf` para direcionar o produtor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Executar o produtor na linha de comando

Para executar o produtor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

O produtor começa a enviar eventos `test`para o centro de eventos no tópico , e imprime os eventos para stdout.

## <a name="run-akka-streams-consumer"></a>Executar consumidor do Akka Streams

Usando o exemplo do consumidor fornecido, receba mensagens do hub de eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="consumer-applicationconf"></a>Consumidor application.conf

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `consumer/src/main/resources/application.conf` para direcionar o consumidor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor na linha de comando

Para executar o consumidor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se o hub de eventos tiver eventos (por exemplo, se o seu `test`produtor também estiver em execução), então o consumidor começa a receber eventos do tópico . 

Confira o [Guia de Kafka do Akka Streams](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para encontrar informações mais detalhadas sobre o Akka Streams.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre event hubs para Kafka, consulte os seguintes artigos:  

- [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integre o Kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Guia de desenvolvedores Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)
