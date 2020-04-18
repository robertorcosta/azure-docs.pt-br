---
title: Guia de desenvolvedores Apache Kafka para Hubs de Eventos
description: Este artigo fornece links para artigos que descrevem como integrar seus aplicativos Kafka com o Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: d90bf8efab28624672dcedb5bf53d45052dd9123
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605122"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Guia de desenvolvedores Apache Kafka para Azure Event Hubs
Este artigo fornece links para artigos que descrevem como integrar seus aplicativos Apache Kafka com o Azure Event Hubs. 

## <a name="overview"></a>Visão geral
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Kafka existentes como uma alternativa para executar seu próprio cluster Kafka. Os Hubs de Eventos dão suporte ao protocolo 1.0 e posterior do Apache Kafka e funciona com aplicativos existentes do Kafka, incluindo o MirrorMaker. Para obter mais informações, consulte [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Inícios rápidos
Você pode encontrar quickstarts no GitHub e neste conjunto de conteúdo que ajuda você a aumentar rapidamente em Event Hubs for Kafka.

### <a name="quickstarts-in-github"></a>Quickstarts no GitHub
Veja as seguintes partidas rápidas no **azure-event-hubs-for-kafka** repo: 

| Idioma/framework do cliente | Descrição | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em C# usando o .NET Core 2.0.</p><p>Esta amostra é baseada no [cliente Apache Kafka .NET da Confluent,](https://github.com/confluentinc/confluent-kafka-dotnet)modificado para uso com Event Hubs for Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em Node.</p><p>Esta amostra usa a biblioteca [nó-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em python.</p><p>Esta amostra é baseada no [cliente Apache Kafka Python da Confluent,](https://github.com/confluentinc/confluent-kafka-python)modificado para uso com Event Hubs for Kafka.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em Go.</p><p>Esta amostra é baseada no [cliente Apache Kafka Golang da Confluent,](https://github.com/confluentinc/confluent-kafka-go)modificado para uso com Event Hubs for Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em Go usando a biblioteca [de clientes Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Este quickstart mostrará como criar e conectar-se a um ponto final do Event Hubs Kafka usando o CLI que vem empacotado com a distribuição Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | Kafkacat é um consumidor e produtor não-JVM baseado em librdkafka, popular devido à sua velocidade e pequena pegada. Este quickstart contém uma configuração de amostra e vários comandos kafkacat de amostra simples. | 
 
### <a name="quickstarts-in-docs"></a>Quickstarts em DOCS
Veja o quickstart: [Fluxo de dados com hubs de eventos usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) neste conjunto de conteúdo, que fornece instruções passo a passo sobre como transmitir em Hubs de Eventos. Você aprende a usar seus produtores e consumidores para conversar com o Event Hubs com apenas uma mudança de configuração em seus aplicativos. 


## <a name="tutorials"></a>Tutoriais 

### <a name="tutorials-in-github"></a>Tutoriais no GitHub
Veja os tutoriais a seguir no GitHub:

| Tutorial | Descrição | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Este tutorial mostra como conectar os Fluxos Akka aos Hubs de Eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Existem dois tutoriais separados usando linguagens de programação **Java** e **Scala.** | 
| [Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Este documento irá levá-lo através da integração kafka connect com a azure Event Hubs e implantar conectores básicos FileStreamSource e FileStreamSink. Embora esses conectores não sejam feitos para uso de produção, eles demonstram um cenário de conexão kafka de ponta a ponta onde o Azure Event Hubs se disfarça de corretor kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Este documento irá levá-lo através da integração de Filebeat e Event Hubs através da saída Kafka do Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Este tutorial mostrará como conectar o Apache Flink aos Hubs de Eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. | 
| [Fluente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Este documento irá levá-lo através da integração `out_kafka` Fluentd e Event Hubs usando o plugin de saída para Fluentd. |
| [Interoperabilidade](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Este tutorial mostra como trocar eventos entre consumidores e produtores usando diferentes protocolos. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Este tutorial irá levá-lo através da integração logstash com hubs de eventos habilitados para Kafka usando plugins de entrada/saída Logstash Kafka. | 
| [Criador de espelhos](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Este tutorial mostra como um hub de eventos e o Kafka MirrorMaker podem integrar um pipeline Kafka existente no Azure, espelhando o fluxo de entrada Kafka no serviço Event Hubs. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Este tutorial mostrará como conectar o Apache NiFi a um namespace do Event Hubs. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Os quickstarts mostram como criar e conectar-se a um ponto final do Event Hubs Kafka usando um exemplo de produtor e consumidor escrito em linguagens de programação Go e Java. |
| [Registro de Esquemade Confluente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Este tutorial irá levá-lo através da integração de Schema Registry e Event Hubs for Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Este tutorial mostrará como conectar seu aplicativo Spark a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters Kafka. | 

### <a name="tutorials-in-docs"></a>Tutoriais em DOCS
Veja também o tutorial: [Processe o Apache Kafka para eventos do Event Hubs usando análises de fluxo](event-hubs-kafka-stream-analytics.md) neste conjunto de conteúdo, que mostra como transmitir dados em Hubs de Eventos e processá-los com o Azure Stream Analytics.

## <a name="how-to-guides"></a>Guias de instruções
Veja os seguintes guias de como fazer em nossa documentação:

| Artigo | Descrição | 
| ------- | ----------- | 
| [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md) | Mostra como espelhar um corretor Kafka em um hub de eventos usando Kafka MirrorMaker. |
| [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md) | O programa conecta o aplicativo Spark ao Event Hubs para streaming em tempo real. |
| [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md) | Mostra como conectar o Apache Flink a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. |
| [Integre o Apache Kafka Connect com um hub de eventos (Preview)](event-hubs-kafka-connect-tutorial.md) | Orienta você a integrar o Kafka Connect com um hub de eventos e implantar conectores básicos fileStreamSource e FileStreamSink. |
| [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md) | Mostra como conectar a Akka Streams a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. |
| [Use o Starter de bota de mola para Apache Kafka com hubs de eventos azure](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Demonstra como configurar um Spring Cloud Stream Binder baseado em Java criado com o Iniciador de Inicialização de Inicialização de Inicialização de Mola para usar o Apache Kafka com hubs de eventos Azure. |

## <a name="next-steps"></a>Próximas etapas
Revise amostras no repo do GitHub [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) em pastas quickstart e tutoriais.

Veja também os seguintes artigos:

- [Guia de solução de problemas apache Kafka para Hubs de Eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de migração Apache Kafka para Hubs de Eventos](apache-kafka-migration-guide.md)



