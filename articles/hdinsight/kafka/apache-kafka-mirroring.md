---
title: Tópicos sobre espelho do Apache Kafka – Azure HDInsight
description: Saiba como usar o recurso de espelhamento do Apache Kafka para manter uma réplica de um cluster Kafka no HDInsight espelhando tópicos para um cluster secundário.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425877"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Use MirrorMaker para replicar tópicos do Apache Kafka com Kafka no HDInsight

Saiba como usar o recurso de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. O espelhamento pode ser executado como um processo contínuo ou usado de forma intermitente como um método de migração de dados de um cluster para outro.

Neste exemplo, o espelhamento é usado para replicar tópicos entre dois clusters de HDInsight. Ambos os clusters estão em diferentes redes virtuais em diferentes data centers.

> [!WARNING]  
> O espelhamento não deve ser considerado um meio de obter tolerância a falhas. A compensação para itens dentro de um tópico é diferente entre os clusters primário e secundário, de modo que os clientes não podem usar os dois de forma intercambiável.
>
> Se estiver preocupado com a tolerância a falhas, você deverá definir a replicação para os tópicos no cluster. Para saber mais, consulte [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelhamento do Apache Kafka

O espelhamento funciona usando a ferramenta [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte do Apache Kafka) para consumir registros de tópicos no cluster principal e, em seguida, criar uma cópia local no cluster secundário. O MirrorMaker usa um (ou mais) *consumidores* que lêem do cluster primário e um *produtor* que escreve para o cluster local (secundário).

A configuração de espelhamento mais útil para recuperação de desastres utiliza clusters Kafka em diferentes regiões do Azure. Para isso, as redes virtuais onde os clusters residem são espiadas juntas.

O diagrama a seguir ilustra o processo de espelhamento e como a comunicação flui entre clusters:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Os clusters primário e secundário podem ser diferentes no número de nós e partições, e as compensações dentro dos tópicos também são diferentes. O espelhamento mantém o valor de chave que é usado para particionamento. Assim, a ordem de registros é preservada por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento entre limites de rede

Se você precisa de espelhamento entre clusters Kafka em redes diferentes, há as seguintes considerações adicionais:

* **Gateways**: As redes devem ser capazes de se comunicar no nível TCP/IP.

* **Endereçamento do servidor**: Você pode optar por abordar seus nós de cluster usando seus endereços IP ou nomes de domínio totalmente qualificados.

    * **Endereços IP**: Se você configurar seus clusters Kafka para usar publicidade de endereço IP, você pode prosseguir com a configuração de espelhamento usando os endereços IP dos nós do corretor e dos nós do zookeeper.
    
    * **Nomes de domínio**: Se você não configurar seus clusters Kafka para publicidade de endereçoIP, os clusters devem ser capazes de se conectar uns aos outros usando Nomes de Domínio Totalmente Qualificados (FQDNs). Isso requer um servidor DNS (Domain Name System, sistema de nomes de domínio) em cada rede configurada para encaminhar solicitações para as outras redes. Ao criar uma Rede Virtual do Azure, em vez de usar o DNS automático fornecido com a rede, você deve especificar um servidor DNS personalizado e o endereço IP do servidor. Depois que a Rede Virtual for criada, você deverá criar uma Máquina Virtual do Azure que use esse endereço IP e instalar e configurar o software DNS nela.

    > [!WARNING]  
    > Crie e configure o servidor DNS personalizado antes de instalar o HDInsight na Rede Virtual. Não é necessária configuração adicional para que o HDInsight use o servidor DNS configurado para a Rede Virtual.

Para obter mais informações sobre como conectar duas Redes Virtuais do Azure, confira [Configurar uma conexão de VNet para VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura de espelhamento

Esta arquitetura possui dois clusters em diferentes grupos de recursos e redes virtuais: uma **primária** e **secundária.**

### <a name="creation-steps"></a>Etapas de criação

1. Crie dois novos grupos de recursos:

    |Grupo de recursos | Location |
    |---|---|
    | kafka-primary-rg | Centro dos EUA |
    | kafka-secundário-rg | Centro-Norte dos EUA |

1. Crie uma nova rede virtual **kafka-primary-vnet** em **kafka-primary-rg**. Deixe as configurações padrão.
1. Crie uma nova rede virtual **kafka-secundária-vnet** em **kafka-secundário-rg**, também com configurações padrão.

1. Crie dois novos clusters Kafka:

    | Nome do cluster | Grupo de recursos | Rede Virtual | Conta de Armazenamento |
    |---|---|---|---|
    | kafka-cluster primário | kafka-primary-rg | kafka-primária-vnet | kafkaprimarystorage |
    | kafka-cluster secundário | kafka-secundário-rg | kafka-secundário-vnet | kafkasecondarystorage |

1. Crie peerings de rede virtuais. Esta etapa criará dois peerings: um de **kafka-primary-vnet** para **kafka-secundário-vnet** e um de volta de **kafka-secundário-vnet** para **kafka-primary-vnet**.
    1. Selecione a rede virtual **kafka-primary-vnet.**
    1. Selecione **Peerings** em **Configurações**.
    1. Selecione **Adicionar**.
    1. Na tela **Adicionar peering, digite** os detalhes como mostrado na captura de tela abaixo.

        ![HDInsight Kafka adicionar vnet peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Configurar publicidade IP

Configure a publicidade IP para permitir que um cliente se conecte usando endereços IP de corretora em vez de nomes de domínio.

1. Vá ao painel Ambari para `https://PRIMARYCLUSTERNAME.azurehdinsight.net`obter o cluster primário: .
1. Selecione **Serviços** > **Kafka**. CliSelectck a guia **Configs.**
1. Adicione as seguintes linhas de configuração à seção **de modelo kafka-env** inferior. Selecione **Salvar**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Digite uma nota na tela **Salvar configuração** e clique **em Salvar**.
1. Se você for solicitado com aviso de configuração, clique **em Proceder de qualquer maneira**.
1. Selecione **Ok** nas alterações de **configuração de salvar**.
1. Selecione **Reiniciar** > **tudo afetado** na notificação necessária de **reinicialização.** Selecione **Confirmar Reiniciar Tudo**.

    ![Apache Ambari reiniciar todos os afetados](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configure Kafka para ouvir em todas as interfaces de rede.
    
1. Mantenha-se na guia **Configs** em **Services** > **Kafka**. Na seção **Kafka Broker** defina a propriedade dos **ouvintes** para `PLAINTEXT://0.0.0.0:9092`.
1. Selecione **Salvar**.
1. Selecione **Reiniciar**e **Confirmar Reiniciar Tudo**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Registros de endereços IP do Corretor de Registros e endereços Zookeeper para cluster principal.

1. Selecione **Hosts** no painel Ambari.
1. Anote os Endereços IP para corretores e zookeepers. Os nódulos corretores **têm wn** como as duas primeiras letras do nome hospedeiro, e os nódulos do zookeeper têm **zk** como as duas primeiras letras do nome hospedeiro.

    ![Endereços IP de exibição do Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Repita as três etapas anteriores para o segundo cluster **kafka-secundário-cluster**: configure publicidade IP, defina ouvintes e anote os endereços IP do Corretor e do Zookeeper.

## <a name="create-topics"></a>Criar tópicos

1. Conecte-se ao cluster **principal** usando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua**sshuser** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **PRIMARYCLUSTER** pelo nome base usado ao criar o cluster.

    Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use o comando a seguir para criar uma variável com os hosts Apache Zookeeper para o cluster principal. As strings `ZOOKEEPER_IP_ADDRESS1` like devem ser substituídas pelos endereços `10.23.0.11` IP `10.23.0.7`reais gravados anteriormente, tais como e . Se você estiver usando a resolução FQDN com um servidor DNS personalizado, siga [estas etapas](apache-kafka-get-started.md#getkafkainfo) para obter nomes de corretores e zookeepers.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Para criar um tópico nomeado `testtopic`, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Use o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta contém `testtopic`.

1. Use o seguinte para visualizar as informações do host zookeeper para este cluster (o **principal):**

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Isso retorna informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salve essas informações. É usado na próxima seção.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Conecte-se ao cluster **secundário** usando uma sessão SSH diferente:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua**sshuser** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **o CLUSTER SECUNDÁRIO** pelo nome usado ao criar o cluster.

    Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Um `consumer.properties` arquivo é usado para configurar a comunicação com o cluster **primário.** Para criar o arquivo, use o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Use o seguinte texto como o conteúdo do arquivo `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** pelos endereços IP do Zookeeper do cluster **principal.**

    Este arquivo descreve as informações do consumidor a serem usados ao ler do cluster Kafka primário. Para obter mais informações de configuração do consumidor, confira [Configurações de Consumidor](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para salvar o arquivo, use **Ctrl + X**, **Y** e, em seguida, **Enter**.

1. Antes de configurar o produtor que se comunica com o cluster secundário, configure uma variável para os endereços IP do cluster **secundário.** Use os seguintes comandos para criar essa variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O `echo $SECONDARY_BROKERHOSTS` comando deve retornar informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Um `producer.properties` arquivo é usado para comunicar o cluster **secundário.** Para criar o arquivo, use o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Use o seguinte texto como o conteúdo do arquivo `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** pelos endereços IP do corretor usados na etapa anterior.

    Para obter mais informações de configuração produtor, confira [Configurações de Produtor](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

1. Use os seguintes comandos para criar uma variável de ambiente com os endereços IP dos hosts Zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A configuração padrão para Kafka no HDInsight não permite a criação automática de tópicos. Você deve usar uma das seguintes opções antes de iniciar o processo de Espelhamento:

    * **Crie os tópicos no cluster secundário**: Esta opção também permite definir o número de partições e o fator de replicação.

        Você pode criar tópicos com antecedência usando o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Substitua `testtopic` pelo nome do tópico a ser criado.

    * **Configure o cluster para criação automática de tópicos**: Esta opção permite que o MirrorMaker crie tópicos automaticamente, no entanto, pode criá-los com um número diferente de partições ou fator de replicação do que o tópico principal.

        Para configurar o cluster secundário para criar automaticamente tópicos, execute essas etapas:

        1. Vá para o painel Ambari para `https://SECONDARYCLUSTERNAME.azurehdinsight.net`o cluster secundário: .
        1. Clique **em Serviços** > **Kafka**. Selecione a guia **Configurações** .
        1. No __campo Filtro,__ digite `auto.create`um valor de . Isso filtrará a lista de propriedades e exibirá a configuração `auto.create.topics.enable`.
        1. Altere o valor de `auto.create.topics.enable` para true e, em seguida, selecione __Salvar__. Adicione uma nota e selecione __Salvar__ novamente.
        1. Selecione o serviço __Kafka,__ selecione __Reiniciar__e selecione __Reiniciar todos os afetados__. Quando solicitado, __selecione Confirmar reiniciar tudo__.

        ![kafka habilitar tópicos de criação automática](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. A partir da conexão SSH ao cluster **secundário,** use o seguinte comando para iniciar o processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros usados neste exemplo são:

    |Parâmetro |Descrição |
    |---|---|
    |--consumidor.config|Especifica o arquivo que contém as propriedades do consumidor. Essas propriedades são usadas para criar um consumidor que lê a partir do cluster *kafka primário.*|
    |--produtor.config|Especifica o arquivo que contém as propriedades do produtor. Essas propriedades são usadas para criar um produtor que escreve para o cluster Kafka *secundário.*|
    |--lista branca|Uma lista de tópicos que o MirrorMaker replica do cluster primário para o secundário.|
    |--num.streams|Número de threads de consumidor a serem criados.|

    O consumidor no nó secundário está agora esperando para receber mensagens.

2. A partir da conexão SSH ao cluster **principal,** use o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando você chega a uma linha em branco com um cursor, digite algumas mensagens de texto. As mensagens são enviadas ao tópico no cluster **principal.** Depois de concluído, use **Ctrl + C** para finalizar o processo de produtor.

3. A partir da conexão SSH ao cluster **secundário,** use **Ctrl + C** para encerrar o processo MirrorMaker. O processo pode levar vários segundos para finalizar. Para verificar se as mensagens foram replicadas para o secundário, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos agora inclui `testtopic`, que é criada quando o MirrorMaster espelha o tópico do cluster primário para o secundário. As mensagens recuperadas do tópico são as mesmas que você inseriu no cluster principal.

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

As etapas deste documento criaram clusters em diferentes grupos de recursos do Azure. Para excluir todos os recursos criados, você pode excluir os dois grupos de recursos criados: **kafka-primary-rg** e **kafka-secondary_rg**. A exclusão dos grupos de recursos remove todos os recursos criados seguindo este documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o [MirrorMake](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)r para criar uma réplica de um cluster [Apache Kafka](https://kafka.apache.org/). Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Documentação do Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Kafka Mirror Maker Melhores Práticas](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conectar-se ao Apache Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
