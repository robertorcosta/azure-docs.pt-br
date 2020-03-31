---
title: Otimização de desempenho para clusters do Apache Kafka HDInsight
description: Fornece uma visão geral das técnicas para otimizar as cargas de trabalho apache kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494916"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para clusters do Apache Kafka HDInsight

Este artigo dá algumas sugestões para otimizar o desempenho de suas cargas de trabalho Apache Kafka no HDInsight. O foco é ajustar a configuração do produtor e do corretor. Existem diferentes formas de medir o desempenho, e as otimizações que você aplica dependerão das necessidades do seu negócio.

## <a name="architecture-overview"></a>Visão geral da arquitetura

Tópicos kafka são usados para organizar registros. Os registros são produzidos por produtores e consumidos por consumidores. Os produtores enviam registros para os corretores kafka, que então armazenam os dados. Cada nó de trabalho no cluster HDInsight é um agente do Kafka.

Registros de partição de tópicos entre agentes. Ao consumir registros, você pode usar um consumidor por partição a fim de alcançar o processamento paralelo dos dados.

A replicação é usada para duplicar partições entre os nós. Isso protege contra paralisações de nó (corretor). Uma única partição entre o grupo de réplicas é designada como líder de partição. O produtor tráfego é roteado para o preenchimento de cada nó, usando o estado gerenciado pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar seu cenário

O desempenho de Apache Kafka tem dois aspectos principais : rendimento e latência. Throughput é a taxa máxima na qual os dados podem ser processados. O rendimento mais alto geralmente é melhor. Latência é o tempo necessário para que os dados sejam armazenados ou recuperados. A latência mais baixa geralmente é melhor. Encontrar o equilíbrio certo entre throughput, latência e o custo da infra-estrutura do aplicativo pode ser desafiador. Seus requisitos de desempenho provavelmente corresponderão a uma das três situações comuns a seguir, com base em se você precisa de alta taxa de rendimento, baixa latência ou ambos:

* Alta ebturação, baixa latência. Este cenário requer alta e baixa latência (~100 milissegundos). Um exemplo desse tipo de aplicativo é o monitoramento da disponibilidade do serviço.
* Alta ebturação, alta latência. Este cenário requer alta taxa de rendimento (~1,5 GBps), mas pode tolerar maior latência (< 250 ms). Um exemplo desse tipo de aplicativo é a ingestão de dados de telemetria para processos quase em tempo real, como aplicativos de segurança e detecção de intrusões.
* Baixa remuneração, baixa latência. Este cenário requer baixa latência (< 10 ms) para processamento em tempo real, mas pode tolerar menor throughput. Um exemplo desse tipo de aplicação é a ortografia on-line e verificações gramaticais.

## <a name="producer-configurations"></a>Configurações do produtor

As seções a seguir destacarão algumas das propriedades de configuração mais importantes para otimizar o desempenho de seus produtores kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte [a documentação Apache Kafka sobre as configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamanho do lote

Os produtores do Apache Kafka reúnem grupos de mensagens (chamados lotes) que são enviadas como uma unidade para serem armazenadas em uma única partição de armazenamento. Tamanho do lote significa o número de bytes que devem estar presentes antes que esse grupo seja transmitido. Aumentar `batch.size` o parâmetro pode aumentar o throughput, pois reduz a sobrecarga de processamento das solicitações de rede e IO. carga leve, o aumento do tamanho do lote pode aumentar a latência de envio de Kafka à medida que o produtor espera que um lote esteja pronto. carga pesada, recomenda-se aumentar o tamanho do lote para melhorar a produtividade e a latência.

### <a name="producer-required-acknowledgments"></a>Produtor exigiu reconhecimentos

A configuração necessária `acks` do produtor determina o número de reconhecimentos exigidos pelo líder de partição antes que uma solicitação de gravação seja considerada concluída. Esta configuração afeta a confiabilidade dos `0` `1`dados `-1`e leva valores de , ou . O valor `-1` de significa que um reconhecimento deve ser recebido de todas as réplicas antes que a gravação seja concluída. A `acks = -1` configuração oferece garantias mais fortes contra a perda de dados, mas também resulta em maior latência e menor rendimento. Se os requisitos do aplicativo `acks = 0` exigirem maior rendimento, tente definir ou `acks = 1`. Tenha em mente que não reconhecer todas as réplicas pode reduzir a confiabilidade dos dados.

### <a name="compression"></a>Compactação

Um produtor kafka pode ser configurado para compactar mensagens antes de enviá-las aos corretores. A `compression.type` configuração especifica o codec de compressão a ser usado. Os codecs de compressão suportados são "gzip", "snappy" e "lz4". A compressão é benéfica e deve ser considerada se houver uma limitação na capacidade do disco.

Entre os dois codecs de `gzip` compressão comumente usados, e `snappy`, `gzip` tem uma maior taxa de compressão, o que resulta em menor uso de disco ao custo de maior carga da CPU. O `snappy` codec fornece menos compactação com menos sobrecarga da CPU. Você pode decidir qual codec usar com base nas limitações do disco do corretor ou da CPU do produtor. `gzip`pode comprimir dados a uma `snappy`taxa cinco vezes maior do que .

O uso da compactação de dados aumentará o número de registros que podem ser armazenados em um disco. Também pode aumentar a sobrecarga da CPU nos casos em que há uma incompatibilidade entre os formatos de compressão que estão sendo usados pelo produtor e pelo corretor. como os dados devem ser compactados antes de enviar e depois descompactados antes do processamento.

## <a name="broker-settings"></a>Configurações do corretor

As seções a seguir destacarão algumas das configurações mais importantes para otimizar o desempenho de seus corretores Kafka. Para obter uma explicação detalhada de todas as configurações do corretor, consulte [a documentação apache kafka sobre as configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Número de discos

Os discos de armazenamento têm IOPS limitado (Operações de entrada/saída por segundo) e bytes de leitura/gravação por segundo. Ao criar novas partições, kafka armazena cada nova partição no disco com menos partições existentes para equilibrá-las nos discos disponíveis. Apesar da estratégia de armazenamento, ao processar centenas de réplicas de partição em cada disco, Kafka pode facilmente saturar o throughput de disco disponível. A troca aqui é entre a eda de tempo e o custo. Se o seu aplicativo exigir maior throughput, crie um cluster com mais discos gerenciados por corretora. No momento, o HDInsight não suporta a adição de discos gerenciados a um cluster em execução. Para obter mais informações sobre como configurar o número de discos gerenciados, consulte [Configurar armazenamento e escalabilidade para Apache Kafka no HDInsight](apache-kafka-scalability.md). Entenda as implicações de custo do aumento do espaço de armazenamento para os nós em seu cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e partições

Os produtores de Kafka escrevem para tópicos. Os consumidores kafka lêem a partir de tópicos. Um tópico está associado a um log, que é uma estrutura de dados no disco. Kafka anexa registros de um produtor até o final de um registro de tópicos. Um registro de tópico consiste em muitas partições que estão espalhadas por vários arquivos. Esses arquivos, por sua vez, estão espalhados por vários nós de cluster Kafka. Os consumidores leem os tópicos de Kafka em sua cadência e podem escolher sua posição (offset) no registro de tópicos.

Cada partição kafka é um arquivo de log no sistema, e os segmentos do produtor podem gravar em vários logs simultaneamente. Da mesma forma, uma vez que cada segmento de consumidor lê mensagens de uma partição, o consumo de várias partições também é manipulado em paralelo.

Aumentar a densidade de partição (o número de partições por corretor) adiciona uma sobrecarga relacionada às operações de metadados e por solicitação/resposta de partição entre o líder de partição e seus seguidores. Mesmo na ausência de fluxo de dados, as réplicas de partição ainda buscam dados dos líderes, o que resulta em processamento extra para envio e recebimento de solicitações pela rede.

Para os clusters Apache Kafka 1.1 e superior no HDInsight, recomendamos que você tenha um máximo de 1000 partições por corretor, incluindo réplicas. O aumento do número de partições por corretora diminui o throughput e também pode causar indisponibilidade de tópicos. Para obter mais informações sobre o suporte à partição de Kafka, consulte [o post oficial do blog Apache Kafka sobre o aumento do número de partições suportadas na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para obter detalhes sobre a modificação de tópicos, consulte [Apache Kafka: modificando tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

Fator de replicação mais alto resulta em solicitações adicionais entre o líder de partição e seguidores. Consequentemente, um fator de replicação mais alto consome mais disco e CPU para lidar com solicitações adicionais, aumentando a latência de gravação e diminuindo o rendimento.

Recomendamos que você use pelo menos 3x de replicação para Kafka no Azure HDInsight. A maioria das regiões do Azure tem três domínios de falha, mas em regiões com apenas dois domínios de falha, os usuários devem usar a replicação 4x.

Para obter mais informações sobre a replicação, consulte [Apache Kafka: replicação](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: fator de replicação crescente](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Próximas etapas

* [Processando trilhões de eventos por dia com o Apache Kafka no Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
