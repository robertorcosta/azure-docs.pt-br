---
title: Integrar-se com o Apache Kafka Connect – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Apache Spark com Hubs de Eventos do Azure para Kafka.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: df7198b68a083abf9be4ffe88e7a5dd848b2c535
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119509"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Integrar o suporte do Apache Kafka Connect aos Hubs de Eventos do Azure (versão prévia)
Conforme as necessidades de ingestão das empresas vai aumentando, também vai aumentando a necessidade de fazer a ingestão em vários coletores e fontes externas. O [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) fornece essa estrutura para conectar e importar/exportar dados de ou para qualquer sistema externo, como o MySQL, o HDFS e o sistema de arquivos, por meio de um cluster do Kafka. Este tutorial explica como usar a estrutura do Kafka Connect com os Hubs de Eventos habilitado para Kafka.

Este tutorial percorre a integração do Kafka Connect com um hub de eventos do Azure habilitado para Kafka e a implantação de conectores FileStreamSource e FileStreamSink básicos. Esse recurso está atualmente na visualização. Embora esses conectores não sirvam para uso em produção, eles demonstram um cenário Kafka Connect de ponta a ponta em que os Hubs de Eventos do Azure atuam como um agente do Kafka.

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar o Kafka Connect para os Hubs de Eventos
> * Executar o Kafka Connect
> * Criar conectores

## <a name="prerequisites"></a>Prerequisites
Para concluir este passo a passo, é necessário atender aos seguintes pré-requisitos:

- Assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka (versão 1.1.1, versão do Scala 2.11), disponível em [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Leia o artigo introdutório [Hubs de Eventos para o Apache Kafka](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [Criando um Hub de Eventos habilitado para Kafka](event-hubs-create.md) para obter instruções sobre como obter um ponto de extremidade Kafka nos Hubs de Eventos. Obtenha a cadeia de conexão dos Hubs de Eventos e o FQDN (nome de domínio totalmente qualificado) para uso posterior. Para obter instruções, confira [Obter uma cadeia de conexão dos Hubs de Eventos](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo
Clone o repositório dos Hubs de Eventos do Azure e navegue até a subpasta tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Configurar o Kafka Connect para os Hubs de Eventos
É necessário um mínimo de reconfiguração para redirecionar a taxa de transferência do Kafka Connect para os Hubs de Eventos.  O exemplo `connect-distributed.properties` abaixo ilustra como configurar o Connect para fazer a autenticação e se comunicar com o ponto de extremidade do Kafka nos Hubs de Eventos:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Executar o Kafka Connect

Nesta etapa, um trabalho do Kafka Connect é iniciado localmente em modo distribuído, usando os Hubs de Eventos para manter o estado do cluster.

1. Salve o arquivo acima `connect-distributed.properties` localmente.  Substitua todos os valores entre chaves.
2. Navegue até o local da versão do Kafka em seu computador.
4. Execute `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  A API REST do trabalho do Connect estará pronta para interação quando você vir `'INFO Finished starting connectors and tasks'`. 

> [!NOTE]
> O Kafka Connect usa a API AdminClient do Kafka para criar automaticamente tópicos com configurações recomendadas, incluindo a compactação. Uma verificação rápida do namespace no portal do Azure revela que os tópicos internos do trabalho do Connect foram criados automaticamente.
>
>Os tópicos internos do Kafka Connect **precisam usar a compactação**.  A equipe dos Hubs de Eventos não será responsável por corrigir configurações inadequadas se os tópicos internos do Connect estiverem configurados incorretamente.

### <a name="create-connectors"></a>Criar conectores
Esta seção percorre com você a configuração de conectores FileStreamSource e FileStreamSink. 

1. Crie um diretório para os arquivos de dados de entrada e saída.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Crie dois arquivos: um arquivo com dados semente que é lido pelo conector FileStreamSource e outro no qual o conector FileStreamSink grava.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Crie um conector FileStreamSource.  Substitua as chaves pelo caminho do diretório base.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Você deve ver o Hub de Eventos `connect-quickstart` em sua instância dos Hubs de Eventos depois de executar o comando acima.
4. Verifique o status do conector de origem.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Opcionalmente, você pode usar o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases) para verificar se os eventos chegaram no tópico `connect-quickstart`.

5. Crie um conector FileStreamSink.  Mais uma vez, substitua as chaves pelo caminho do diretório base.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Verifique o status do conector do coletor.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verifique se dados foram replicados entre os arquivos e se os dados são idênticos nos dois arquivos.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Limpeza
O Kafka Connect cria tópicos de Hub de Eventos para armazenar status, deslocamentos e configurações que persistirão mesmo depois que o cluster do Connect for desativado. A menos que você queira essa persistência, é recomendável excluir esses tópicos. Talvez seja ideal também excluir o Hub de Eventos `connect-quickstart` que foi criado no decorrer deste passo a passo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, consulte o tópico a seguir:  

- [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
- [Hubs de Eventos do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Como criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md)
- [Transmitir para Hubs de Eventos a partir de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um agente do Kafka em um hub de eventos habilitado para Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos habilitado para Kafka](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos habilitado para Kafka](event-hubs-kafka-flink-tutorial.md)
- [Conectar o Akka Streams a um hub de eventos habilitado para Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
