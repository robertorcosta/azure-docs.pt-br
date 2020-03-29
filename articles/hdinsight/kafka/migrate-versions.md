---
title: Migrar cargas de trabalho Apache Kafka para Azure HDInsight 4.0
description: Saiba como migrar as cargas de trabalho apache kafka no HDInsight 3.6 para o HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 66ea8a93723c117a364a188ec05b050e1ef60564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548077"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrar cargas de trabalho Apache Kafka para Azure HDInsight 4.0

O Azure HDInsight 4.0 oferece os componentes de código aberto mais recentes com melhorias significativas em desempenho, conectividade e segurança. Este documento explica como migrar as cargas de trabalho apache kafka no HDInsight 3.6 para o HDInsight 4.0. Depois de migrar suas cargas de trabalho para o HDInsight 4.0, você pode usar muitos dos novos recursos que não estão disponíveis no HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>CAMINHOS de migração HDInsight 3.6 Kafka

O HDInsight 3.6 suporta duas versões do Kafka: 1.0.0 e 1.1.0. O HDInsight 4.0 suporta as versões 1.1.0 e 2.1.0. Dependendo de qual versão do Kafka e qual versão do HDInsight você gostaria de executar, existem vários caminhos de migração suportados. Esses caminhos são explicados abaixo e ilustrados no diagrama a seguir.

* **Execute kafka e HDInsight em versões mais recentes (recomendado)**: Migre um aplicativo HDInsight 3.6 e Kafka 1.0.0 ou 1.1.0 para o HDInsight 4.0 com Kafka 2.1.0 (caminhos D e E abaixo).
* **Execute o HDInsight na versão mais recente, mas kafka apenas em uma versão mais recente**: Migre um aplicativo HDInsight 3.6 e Kafka 1.0.0 para hdInsight 4.0 com Kafka 1.1.0 (caminho B abaixo).
* **Execute o HDInsight na versão mais recente, mantenha a versão Kafka**: Migre um aplicativo HDInsight 3.6 e Kafka 1.1.0 para o HDInsight 4.0 com Kafka 1.1.0 (caminho C abaixo).
* **Execute Kafka em uma versão mais recente, mantenha a versão HDInsight**: Migre um aplicativo Kafka 1.0.0 para 1.1.0 e fique no HDInsight 3.6 (caminho A abaixo). Observe que essa opção ainda exigirá a implantação de um novo cluster. Não é suportado o upgrade da versão Kafka em um cluster existente. Depois de criar um cluster com a versão desejada, migre seus clientes Kafka para usar o novo cluster.

![Atualizar caminhos para Apache Kafka em 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Versões de Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se você migrar de Kafka 1.0.0 para 1.1.0, poderá aproveitar os seguintes novos recursos:

* Melhorias no controlador Kafka aceleram o desligamento controlado, para que você possa reiniciar corretores e se recuperar de problemas mais rapidamente. 
* Melhorias na [lógica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) que permitem que você tenha mais partições (e, portanto, mais tópicos) no cluster. 
* Kafka Connect suporta [cabeçalhos de gravação](https://issues.apache.org/jira/browse/KAFKA-5142) e [expressões regulares](https://issues.apache.org/jira/browse/KAFKA-3073) para tópicos. 

Para obter uma lista completa de atualizações, consulte as notas de [versão do Apache Kafka 1.1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se você migrar para kafka 2.1, você pode aproveitar os seguintes recursos:

* Melhor resiliência do corretor devido a um protocolo de replicação melhorado.
* Nova funcionalidade na API KafkaAdminClient.
* Gerenciamento de cotas configurável.
* Suporte para compactação Zstandard.

Para obter uma lista completa de atualizações, consulte as notas de [versão apache kafka 2.0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e [as notas de versão apache Kafka 2.1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Compatibilidade do cliente Kafka

Os novos corretores Kafka apoiam clientes mais velhos. [KIP-35 - A versão do protocolo de recuperação](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) introduziu um mecanismo para determinar dinamicamente a funcionalidade de um corretor Kafka e [kip-97: A Política de Compatibilidade RPC cliente da Kafka aprimorada](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) introduziu uma nova política de compatibilidade e garantias para o cliente Java. Anteriormente, um cliente Kafka tinha que interagir com um corretor da mesma versão ou uma versão mais recente. Agora, versões mais recentes dos clientes Java e `librdkafka` outros clientes que suportam KIP-35, como podem voltar para tipos de solicitação mais antigos ou jogar erros apropriados se a funcionalidade não estiver disponível.

![Atualizar a compatibilidade do cliente Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Note que isso não significa que o cliente suporta corretores mais antigos.  Para obter mais informações, consulte [Matrix de compatibilidade](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Processo de migração geral

A seguinte orientação de migração pressupõe um cluster Apache Kafka 1.0.0 ou 1.1.0 implantado no HDInsight 3.6 em uma única rede virtual. O corretor existente tem alguns tópicos e está sendo utilizado ativamente por produtores e consumidores.

![Ambiente presumido de Kafka atual](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Para concluir a migração, faça as seguintes etapas:

1. **Implante um novo cluster HDInsight 4.0 e clientes para teste.** Implante um novo cluster HDInsight 4.0 Kafka. Se várias versões do cluster Kafka puderem ser selecionadas, é recomendável selecionar a versão mais recente. Após a implantação, defina alguns parâmetros conforme necessário e crie um tópico com o mesmo nome do ambiente existente. Além disso, defina a criptografia SSL e bring-your-own-key (BYOK) conforme necessário. Em seguida, verifique se ele funciona corretamente com o novo cluster.

    ![Implantar novos clusters HDInsight 4.0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Troque o cluster para o aplicativo do produtor e aguarde até que todos os dados da fila sejam consumidos pelos consumidores atuais.** Quando o novo cluster HDInsight 4.0 Kafka estiver pronto, mude o destino do produtor existente para o novo cluster. Deixe-o como está até que o aplicativo Consumer existente tenha consumido todos os dados do cluster existente.

    ![Trocar cluster para aplicativo de produtor](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Alterne o cluster na aplicação do consumidor.** Depois de confirmar que o aplicativo de consumo existente terminou de consumir todos os dados do cluster existente, mude a conexão para o novo cluster.

    ![Mudar cluster no aplicativo do consumidor](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Remova o antigo cluster e os aplicativos de teste, conforme necessário.** Uma vez que o switch esteja completo e funcionando corretamente, remova o antigo cluster HDInsight 3.6 Kafka e os produtores e consumidores usados no teste conforme necessário.

## <a name="next-steps"></a>Próximas etapas

* [Otimização de desempenho para clusters do Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Quickstart: Crie o cluster Apache Kafka no Azure HDInsight usando o portal Azure](apache-kafka-get-started.md)
