---
title: Apache Kafka com o registro de esquema confluente no Azure HDInsight
description: Implante um cluster Kafka gerenciado do HDInsight com um nó de borda dentro de uma rede virtual e, em seguida, instale o registro de esquema próprio no nó de borda.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 7e17cdca508db81551d988c795bd1235fa729e82
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636853"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka com o registro de esquema confluente no Azure HDInsight

O registro de esquema Kafka fornece serializadores que se conectam a clientes Apache Kafka que lidam com o armazenamento e a recuperação do esquema de mensagens para mensagens Kafka enviadas no formato Avro. Ele costumava ser um projeto de OSS por meio do mesmo, mas agora está sob a [licença da Comunidade confluente](https://www.confluent.io/blog/license-changes-confluent-platform/). O registro de esquema adicionalmente atende às finalidades a seguir:

* Armazene e recupere esquemas para produtores e consumidores.
* Impor a compatibilidade de/Full para trás/avanço nos tópicos.
* Diminua o tamanho da carga enviada para Kafka.

Em um cluster Kafka gerenciado do HDInsight, o registro de esquema normalmente é implantado em um nó de borda para permitir a separação de computação de nós de cabeçalho.

Veja abaixo uma arquitetura representativa de como o registro de esquema é implantado em um cluster HDInsight. O registro de esquema expõe nativamente uma API REST para operações nele.  Produtores e consumidores podem interagir com o registro de esquema de dentro da VNet ou usando o [proxy REST do Kafka](rest-proxy.md).

![Registro de esquema do HDInsight Kafka](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Implantar um Kafka gerenciado do HDInsight com o registro de esquema confluente

Nesta seção, implantamos um cluster Kafka gerenciado do HDInsight com um nó de borda dentro de uma rede virtual e, em seguida, instalamos o registro de esquema de esquemas no nó de borda.  

1. Selecione o botão **Implantar no Azure** abaixo para entrar no Azure e abrir o modelo do Resource Manager.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json" target="_blank">:::image type="icon" source="media/schema-registry/hdi-deploy-to-azure1.png":::</a>

1. No modelo de implantação personalizada, preencha os campos conforme descrito abaixo:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Na lista suspensa, selecione a assinatura do Azure usada para o cluster.|
    |Resource group|Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
    |Região|Na lista suspensa, selecione uma região em que o cluster foi criado.|
    |Nome do cluster|Insira um nome global exclusivo. Ou deixe como está para usar o nome padrão.|
    |Nome de usuário de logon do cluster|Forneça o nome de usuário, o padrão é **admin**.|
    |Senha de logon do cluster|Forneça a senha.|
    |Nome de Usuário SSH|Forneça o nome de usuário, o padrão é **sshuser**.|
    |Senha SSH|Forneça a senha.|

    Deixe os outros campos como estão. Selecione **Examinar + criar** para continuar.

1. Examine os detalhes da implantação e, em seguida, selecione **criar** para inicializar a implantação. A implantação pode levar de 45 minutos para ser concluída.

## <a name="configure-the-confluent-schema-registry"></a>Configurar o registro de esquema próprio

Nesta seção, configuramos o registro de esquema Kafka que instalamos no nó de borda. O registro de esquema de esquemas está localizado em  `/etc/schema-registry/schema-registry.properties` e os mecanismos para iniciar e parar os executáveis de serviço estão localizados na  `/usr/bin/` pasta.

O registro de esquema precisa saber que o serviço Zookeeper pode interagir com o cluster Kafka do HDInsight. Siga as etapas abaixo para obter os detalhes do quorum Zookeeper.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao nó de borda do cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Configurar variável de senha. Substitua senha pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Extraia o nome do cluster com grafia correta de maiúsculas e minúsculas. Execute o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Extraia os hosts Kafka Zookeeper. Execute o seguinte comando:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Anote esse valor, pois ele será usado posteriormente.

1. Extraia os hosts do agente do Kafka. Execute o seguinte comando:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Abra os arquivos de propriedades do registro de esquema no modo de edição. Execute o seguinte comando:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Atualize o valor de `kafkastore.connection.url` com a cadeia de caracteres Zookeeper identificada anteriormente.
    1. Atualize o valor de `debug` para `true` .

    O arquivo de propriedades agora é semelhante a:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Para salvar o arquivo, use **Ctrl + X** , **Y** e, em seguida, **Enter**.

1. Inicie o registro de esquema e aponte-o para usar o arquivo de propriedades do registro de esquema atualizado. Execute os seguintes comandos:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Com o registro de esquema em execução em uma sessão SSH, inicie outra janela SSH.  Registre uma nova versão de um esquema sob o assunto "Kafka-Key" e observe a saída:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registre uma nova versão de um esquema sob o assunto "Kafka-Value" e anote a saída:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Listar todos os assuntos e verificar a saída:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Talvez você queira experimentar alguns outros [comandos avançados listados aqui](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Enviar e consumir dados Avro do Kafka usando o registro de esquema

Nesta seção, vamos ler dados de entrada padrão e escrevê-los em um tópico de Kafka em um formato. Em seguida, vamos ler os dados dos tópicos do Kafka usando um consumidor com um formatador Avro para transformar esses dados em formato legível.

1. Criar um tópico do Kafka `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Use o **produtor do console do Kafka Avro** para criar um esquema, atribuir o esquema ao tópico e começar a enviar dados para o tópico no formato Avro. Verifique se o tópico Kafka na etapa anterior foi criado com êxito e se **$KAFKABROKERS** tem um valor nele.

    O esquema que estamos enviando é um par chave: valor

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Use o comando abaixo para iniciar o **produtor do console do Kafka Avro** :

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Quando o produtor estiver pronto para aceitar mensagens, comece a enviar as mensagens no formato de esquema de Avro predefinido. Use a tecla Tab para criar o espaçamento entre a chave e o valor.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Tente inserir dados aleatórios que não são de esquema no produtor do console para ver como o produtor agora permite que todos os dados que não estejam em conformidade com o esquema Avro predefinido.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Em uma tela diferente, inicie o consumidor do console do Kafka Avro

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Você deve começar a ver a saída abaixo:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Próximas etapas

* [Usar as APIs de produtor e consumidor do Apache Kafka](apache-kafka-producer-consumer-api.md)