---
title: Gravações aceleradas do Azure HDInsight para Apache HBase
description: Dá uma visão geral do recurso Azure HDInsight Accelerated Writes, que usa discos gerenciados premium para melhorar o desempenho do Registro de Gravação Antecipada do Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764607"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Gravações aceleradas do Azure HDInsight para Apache HBase

Este artigo fornece o fundo do recurso **Accelerated Writes** para Apache HBase no Azure HDInsight, e como ele pode ser usado efetivamente para melhorar o desempenho de gravação. **Accelerated Writes** usa [discos ssd premium do Azure](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do Registro de Gravação Antecipada (WAL) do Apache HBase Write Ahead Log (WAL). Para saber mais sobre o Apache HBase, consulte [o que é Apache HBase no HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura HBase

No HBase, uma **linha** consiste em uma ou mais **colunas** e é identificada por uma **tecla de linha**. Várias linhas compõem uma **mesa.** As colunas contêm **células,** que são versões carimbadas do valor nessa coluna. As colunas são agrupadas em **famílias de colunas,** e todas as colunas de uma família de colunas são armazenadas juntas em arquivos de armazenamento chamados **HFiles**.

**As regiões** do HBase são usadas para equilibrar a carga de processamento de dados. O HBase armazena primeiro as linhas de uma tabela em uma única região. As linhas estão espalhadas por várias regiões à medida que a quantidade de dados na tabela aumenta. **Os servidores da região** podem lidar com solicitações para várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Escreva o log de gravação para Apache HBase

O HBase primeiro grava atualizações de dados para um tipo de registro de confirmação chamado Write Ahead Log (WAL). Depois que a atualização é armazenada no WAL, ela é escrita para o **MemStore**na memória . Quando os dados na memória atingem sua capacidade máxima, ele é gravado em disco como um **HFile**.

Se um **RegionServer** falhar ou ficar indisponível antes que o MemStore seja liberado, o Write Ahead Log pode ser usado para reproduzir atualizações. Sem o WAL, se um **RegionServer** falhar antes de liberar atualizações para um **HFile,** todas essas atualizações serão perdidas.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Recurso de gravações aceleradas no Azure HDInsight para Apache HBase

O recurso Accelerated Writes resolve o problema de maiores latências de gravação causadas pelo uso de Write Ahead Logs que estão no armazenamento em nuvem.  O recurso Accelerated Writes para clusters HDInsight Apache HBase, anexa discos gerenciados por SSD premium a cada RegionalServer (nó do trabalhador). Os logs de gravação antecipada são então gravados no Hadoop File System (HDFS) montado nesses discos gerenciados premium em vez de armazenamento em nuvem.  Os discos gerenciados premium usam Discos de Estado Sólido (SSDs) e oferecem excelente desempenho de I/O com tolerância a falhas.  Ao contrário dos discos não gerenciados, se uma unidade de armazenamento cair, ela não afetará outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos gerenciados fornecem baixa latência de gravação e melhor resiliência para seus aplicativos. Para saber mais sobre os discos gerenciados pelo Azure, consulte [Introdução aos discos gerenciados pelo Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Como ativar gravações aceleradas para HBase no HDInsight

Para criar um novo cluster HBase com o recurso Accelerated Writes, siga as etapas em [Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar ao **Passo 3, Armazenamento**. Em **Configurações de metastore,** selecione a caixa de seleção ao lado **de Ativar gravações aceleradas do HBase**. Em seguida, continue com as etapas restantes para a criação de clusters.

![Habilite a opção de gravações aceleradas para HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós operianos. Uma vez criado, você não pode reduzir o cluster para menos de três nós operários.

Lave ou desative suas tabelas do HBase antes de excluir o cluster, para que você não perca os dados do Write Ahead Log.

```
flush 'mytable'
```

```
disable 'mytable'
```

Siga passos semelhantes ao reduzir o cluster: lave suas tabelas e desative suas tabelas para impedir a entrada de dados. Você não pode reduzir seu cluster para menos de três nódulos.

Seguir essas etapas garantirá uma rebaixação de escala bem-sucedida e evitará a possibilidade de um nome entrar em modo de segurança devido a arquivos subreplicados ou temporários.

Se o seu nome entrar em modo de segurança após uma escala para baixo, use comandos hdfs para replicar os blocos sub-replicados e obter hdfs fora do modo de segurança. Essa re-replicação permitirá que você reinicie o HBase com sucesso.

## <a name="next-steps"></a>Próximas etapas

* Documentação oficial do Apache HBase no [recurso Write Ahead Log](https://hbase.apache.org/book.html#wal)
* Para atualizar o cluster HDInsight Apache HBase para usar gravações aceleradas, consulte [Migrar um cluster Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
