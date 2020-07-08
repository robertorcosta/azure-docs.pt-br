---
title: Usar o MirrorMaker do Apache Kafka - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka MirrorMaker para espelhar um cluster do Kafka no AzureEvent Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aea8ebcfa65d5f4c90aa1908d03f0fcde8906bba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320183"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Use Kafka MirrorMaker com Hubs de Eventos para o Apache Kafka

Este tutorial mostra como espelhar um agente do Kafka em um hub de eventos usando o Kafka MirrorMaker.

   ![MirrorMaker Kafka com Hubs de Eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar o Kafka MirrorMaker
> * Executar o Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Uma consideração importante para aplicativos de escala de nuvem modernos é a capacidade de atualizar, melhorar e alterar a infraestrutura sem interromper o serviço. Este tutorial mostra como um hub de eventos e Kafka MirrorMaker podem integrar um pipeline Kafka existente ao Azure por meio do "espelhamento" do fluxo de entrada Kafka no serviço de hubs de eventos. 

Um ponto de extremidade Kafka dos Hubs de Eventos do Azure permite que você se conecte aos Hubs de Eventos do Azure usando o protocolo Kafka (ou seja, clientes Kafka). Ao fazer alterações mínimas em um aplicativo do Kafka, você pode se conectar aos Hubs de Eventos do Azure e aproveitar os benefícios do ecossistema do Azure. Atualmente, os hubs de eventos dão suporte às versões 1,0 e posteriores do Kafka.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de hubs de eventos, clone os hubs de eventos do Azure para o repositório Kafka e navegue até a `mirror-maker` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster Kafka

Use o [guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as configurações desejadas (ou use um cluster Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar o Kafka MirrorMaker

O Kafka MirrorMaker permite o "espelhamento" de um fluxo. Dada a origem e destino dos clusters Kafka, o MirrorMaker garante que todas as mensagens enviadas para o cluster de origem são recebidas por clusters de origem e de destino. Este exemplo mostra como espelhar um cluster Kafka de origem com um hub de eventos de destino. Esse cenário pode ser usado para enviar dados de um pipeline Kafka existente para Hubs de Eventos sem interromper o fluxo de dados. 

Para obter mais informações sobre o Kafka MirrorMaker, consulte o [guia de Espelhamento do Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dê a ele um cluster Kafka como seu consumidor/origem e um hub de eventos como seu produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o arquivo de configuração do consumidor `source-kafka.config`, que informa ao MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora, atualize o arquivo de configuração do produtor `mirror-eventhub.config`, que manda o MirrorMaker enviar os dados duplicados (ou "espelhados") para o serviço de Hubs de Eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de extremidade Kafka dos Hubs de Eventos. O serviço dos Hubs de Eventos requer uma comunicação segura (SASL), que é obtida com a definição das três últimas propriedades na configuração a seguir: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Executar o Kafka MirrorMaker

Execute o script do Kafka MirrorMaker a partir do diretório raiz do Kafka usando os arquivos de configuração atualizados recentemente. Certifique-se de copiar os arquivos de configuração para o diretório raiz do Kafka, ou de atualizar seus caminhos no comando a seguir.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar se os eventos estão atingindo o Hub de eventos, consulte as estatísticas de entrada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/)ou execute um consumidor no Hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados ao cluster Kafka de origem são recebidos pelo cluster Kafka e pelo hub de eventos espelhado. Usando o MirrorMaker e um ponto de extremidade Kafka nos Hubs de Eventos, você pode migrar um pipeline Kafka existente para o serviço de Hubs de Eventos do Azure gerenciado sem alterar o cluster existente ou interromper qualquer fluxo de dados em andamento.

## <a name="samples"></a>Exemplos
Consulte os seguintes exemplos no GitHub:

- [Código de exemplo para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Hubs de eventos do Azure Kafka MirrorMaker em execução em uma instância de contêiner do Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os hubs de eventos para Kafka, consulte os seguintes artigos:  

- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar fluxos do Akka a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)