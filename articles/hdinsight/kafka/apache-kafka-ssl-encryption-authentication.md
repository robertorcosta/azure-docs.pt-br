---
title: Autenticação de & de criptografia Apache Kafka SSL - Azure HDInsight
description: Configure criptografia SSL para comunicação entre clientes Kafka e corretores Kafka, bem como entre corretores Kafka. Configure a autenticação SSL dos clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 4a363caf61046cf39c31ae2d5f35622b7b9109f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129981"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configure criptografia e autenticação de Camada de Soquetes Seguros (SSL) para Apache Kafka no Azure HDInsight

Este artigo mostra como configurar criptografia SSL entre clientes Apache Kafka e corretores Apache Kafka. Ele também mostra como configurar a autenticação de clientes (às vezes referido como SSL bidirecional).

> [!Important]
> Existem dois clientes que você pode usar para aplicativos Kafka: um cliente Java e um cliente de console. Apenas o `ProducerConsumer.java` cliente Java pode usar SSL para produzir e consumir. O cliente `console-producer.sh` produtor de console não trabalha com SSL.

> [!Note] 
> O produtor de consoles HDInsight Kafka com a versão 1.1 não suporta SSL.
## <a name="apache-kafka-broker-setup"></a>Configuração do corretor Apache Kafka

A configuração do corretor Kafka SSL usará quatro VMs de cluster HDInsight da seguinte maneira:

* cabeça 0 - Autoridade de Certificado (CA)
* nó trabalhador 0, 1 e 2 - corretores

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

O resumo do processo de configuração do corretor é o seguinte:

1. As seguintes etapas são repetidas em cada um dos três nós de trabalhadores:

    1. Gere um certificado.
    1. Crie um pedido de assinatura cert.
    1. Envie a solicitação de assinatura do CERT à Autoridade de Certificado (CA).
    1. Faça login no CA e assine a solicitação.
    1. SCP a certidão assinada de volta ao nó do trabalhador.
    1. SCP a certidão pública do AC para o nó do trabalhador.

1. Uma vez que você tenha todos os certificados, coloque os certs na loja cert.
1. Vá para Ambari e altere as configurações.

Use as seguintes instruções detalhadas para concluir a configuração do corretor:

> [!Important]
> Nos seguintes trechos de código wnX é uma abreviação para um dos três nódulos `wn1` `wn2` de trabalhador e deve ser substituído por `wn0`, ou conforme apropriado. `WorkerNode0_Name`e `HeadNode0_Name` devem ser substituídos pelos nomes das respectivas máquinas.

1. Executar a configuração inicial no nó de cabeça 0, que para hdInsight preencherá a função de Autoridade de Certificado (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Realize a mesma configuração inicial em cada um dos corretores (nó súpero 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um dos nós do trabalhador, execute as seguintes etapas usando o trecho de código abaixo.
    1. Crie uma loja de chaves e preencha-a com um novo certificado privado.
    1. Crie uma solicitação de assinatura de certificado.
    1. SCP o pedido de assinatura de certificado ao CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na máquina CA execute o seguinte comando para criar arquivos ca-cert e ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Mude para a máquina CA e assine todas as solicitações de assinatura de cert recebidas:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Enviar os certificados assinados de volta aos nódulos do trabalhador do CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público do CA para cada nó do trabalhador.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó do trabalhador, adicione o certificado público CAs à loja de fundos e armazenamento de chaves. Em seguida, adicione o certificado assinado do próprio nó do trabalhador à loja de chaves

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para usar SSL e reiniciar os agentes

Você já configurou cada corretor Kafka com uma loja de chaves e truststore, e importou os certificados corretos. Em seguida, modifique as propriedades de configuração do Kafka relacionadas usando o Ambari e reinicie os agentes do Kafka.

Execute as seguintes etapas para concluir a modificação à configuração:

1. Entre no portal do Azure e selecione seu cluster do Apache Kafka do Azure HDInsight.
1. Vá para a interface do usuário do Ambari, clicando em **Página Inicial do Ambari** nos **Painéis do cluster**.
1. Em **Agente do Kafka**, defina a propriedade **ouvintes** para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Agente avançado do Kafka**, defina a propriedade **security.inter.broker.protocol** para `SSL`

    ![Editar as propriedades de configuração de SSL do Kafka no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Em **Agente do Kafka personalizado**, defina a propriedade **ssl.client.auth** para `required`. Esta etapa só é necessária se você estiver configurando autenticação e criptografia.

    ![Editar as propriedades de configuração de SSL do Kafka no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Adicione novas propriedades de configuração ao arquivo server.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Vá para Ambari configuração UI e verifique se as novas propriedades aparecem em **Kafka-env Avançado** e na propriedade **modelo kafka-env.**

    Para a versão 3.6 do HDI:

    ![Edição da propriedade modelo kafka-env em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Para a versão 4.0 do HDI:

     ![Edição de propriedade modelo kafka-env em Ambari quatro](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Reinicie todos os agentes do Kafka.
1. Inicie o cliente de admin com opções de produtor e consumidor para verificar se tanto os produtores quanto os consumidores estão trabalhando no porto 9093.

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se você não precisar de autenticação, o resumo das etapas para configurar apenas a criptografia SSL são:

1. Faça login no CA (nó de cabeça ativo).
1. Copiar o CA cert para a máquina cliente da máquina CA (wn0).
1. Faça login na máquina cliente (hn1) `~/ssl` e navegue até a pasta.
1. Importe o CERT ca para a loja fiduciário.
1. Importe o CERT CA para a loja de chaves.

Essas etapas são detalhadas nos seguintes trechos de código.

1. Faça login no nó ca.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copie o ca-cert para a máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Faça login na máquina do cliente (nó de cabeça em espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importe o certificado CA para a loja fiduciário.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importe o CA cert para keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Crie o arquivo `client-ssl-auth.properties`. Ele deve conter as seguintes linhas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> As etapas a seguir são necessárias apenas se você está configurando a criptografia SSL **e** também a autenticação. Se você estiver apenas configurando criptografia, consulte [a configuração do Cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

As quatro etapas a seguir resumem as tarefas necessárias para concluir a configuração do cliente:

1. Faça login na máquina do cliente (nó de cabeça em espera).
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está sendo executada.
1. Mude para a máquina CA (nó de cabeça ativo) para assinar o certificado do cliente.
1. Vá até a máquina cliente (nó de cabeça `~/ssl` de espera) e navegue até a pasta. Copie o certificado autoassinado para o computador cliente.

Os detalhes de cada etapa são dados abaixo.

1. Faça login na máquina do cliente (nó de cabeça em espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Remova qualquer diretório ssl existente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Crie um armazenamento de chaves java e crie uma solicitação de assinatura de certificado. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copie a solicitação de assinatura do certificado ao CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Mude para a máquina CA (nó de cabeça ativo) e assine o certificado do cliente.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copiar cert cliente assinado do CA (nó de cabeça ativo) para a máquina do cliente.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copie o ca-cert para a máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Crie a loja do cliente com cert assinado e importe ca cert na loja de chaves e no truststore:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Criar um `client-ssl-auth.properties`arquivo . Ele deve conter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificação

> [!Note]
> Se o HDInsight 4.0 e o Kafka 2.1 forem instalados, você poderá usar o produtor/consumidores do console para verificar sua configuração. Se não, execute o produtor Kafka no porto 9092 e envie mensagens para o tópico, e depois use o consumidor Kafka na porta 9093 que usa SSL.

### <a name="kafka-21-or-above"></a>Kafka 2.1 ou superior

1. Crie um tópico se ele ainda não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Inicie o produtor do `client-ssl-auth.properties` console e forneça o caminho para como um arquivo de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Abra outra conexão ssh para a máquina cliente `client-ssl-auth.properties` e inicie o console consumidor e forneça o caminho para como um arquivo de configuração para o consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Crie um tópico se ele ainda não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Inicie o produtor do console e forneça o caminho para propriedades cliente-ssl-auth.como um arquivo de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Abra outra conexão ssh para a máquina cliente `client-ssl-auth.properties` e inicie o console consumidor e forneça o caminho para como um arquivo de configuração para o consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Próximas etapas

* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
