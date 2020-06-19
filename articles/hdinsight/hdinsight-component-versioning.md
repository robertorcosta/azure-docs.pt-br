---
title: Componentes e versões do Apache Hadoop - Azure HDInsight
description: Saiba mais sobre componentes e versões do Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/20/2020
ms.openlocfilehash: 036548d61dbe6a6465ca28c72eacb48e61386577
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846215"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Componentes e versões do Apache Hadoop disponíveis para o Azure HDInsight

Neste artigo, você aprenderá sobre os componentes e versões do ambiente do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight e o Enterprise Security Package. Você também aprenderá como verificar as versões dos componentes do Hadoop no HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Componentes do Apache disponíveis com diferentes versões do HDInsight

O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Em 4 de abril de 2017, a versão do cluster padrão usada pelo Azure HDInsight era 3.6.

As versões do componente associadas às versões do cluster HDInsight são listadas na tabela a seguir.

> [!NOTE]
> A versão padrão para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar clusters com o SDK do .NET com o Azure PowerShell e a CLI Clássica do Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (padrão)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 na ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| HBase no Apache           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| O Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| O Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4           | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 2.1           | 1,1                        |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0: O Spark 2.4 e o Kafka 2.1 têm suporte completo. No entanto, o Spark 2.3 e o Kafka 1.1 não são mais mantidos. HDInsight 3.6: O Spark 2.3 e o Kafka 1.1 têm suporte completo.  

## <a name="check-for-current-apache-component-version-information"></a>Verificar informações atuais de versão do componente do Apache

As versões de componente do ambiente Hadoop associadas às versões do cluster HDInsight podem ser alteradas com atualizações ao HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para obter mais informações, confira [Documentação do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Notas de versão

Para ver notas adicionais sobre as versões mais recentes do HDInsight, confira [Notas sobre a versão do HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Expiração do suporte e desativação para versões do HDInsight

**Expiração do suporte** significa que a Microsoft não oferece mais suporte para a versão específica do HDInsight. Além disso, ele não está mais disponível por meio do portal do Azure para criação de clusters. Essas versões ainda podem ser criadas usando o CLI do Azure ou os diversos SDKs.

**Desativação** significa que os clusters existentes de uma versão do HDInsight continuam a ser executados como estão. Novos clusters desta versão não podem ser criados por nenhum meio, o que inclui a CLI e os SDKs. Outros recursos de plano de controle, como dimensionamento manual e dimensionamento automático, também poderão não funcionar após a desativação da versão. O suporte não está disponível para versões desativadas.

As tabelas a seguir listam as versões do HDInsight. As datas de expiração do suporte e de desativação também são fornecidas quando elas são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

Esta tabela lista as versões do HDInsight que estão disponíveis no portal do Azure, bem como por outros métodos de implantação como o PowerShell e o SDK do .NET.

| Versão do HDInsight | SO da VM | Data de liberação | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no portal do Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 31 de dezembro de 2020 |31 de dezembro de 2020 |Sim |Sim |

O suporte do Spark 2.1, 2.2 e Kafka 1.0 expira em 30 de junho de 2020.

> [!NOTE]
> Depois que o suporte para uma versão expirar, ela poderá não estar disponível por meio do portal do Azure. As versões do cluster continuarão disponíveis usando o parâmetro **Version** no comando do Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e o SDK .NET até a data de baixa da versão.

### <a name="retired-versions"></a>Versões desativadas

Esta tabela lista as versões do HDInsight que não estão disponíveis no portal do Azure.

| Versão do HDInsight | Versão do HDP | SO da VM | Data de liberação | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no portal do Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de setembro de 2016 |5 de setembro de 2017 |28 de junho de 2018 |Sim |Não |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de março de 2016 |29 de dezembro de 2016 |9 de janeiro de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2017 |Sim |Não |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 de fevereiro de 2015 |1º de março de 2016 |1º de abril de 2017 |Sim |Não |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 de junho de 2014 |18 de maio de 2015 |30 de junho de 2016 |Sim |Não |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 de fevereiro de 2014 |17 de setembro de 2014 |30 de junho de 2015 |Sim |Não |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 de outubro de 2013 |12 de maio de 2014 |31 de maio de 2015 |Sim |Não |
| HDInsight 1.6 |HDP 1.1 | |28 de outubro de 2013 |26 de abril de 2014 |31 de maio de 2015 |Não |Não |

> [!NOTE]
> Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight versão 2.1 e posterior. Eles não estão disponíveis para clusters do HDInsight versão 1.6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões do cluster HDInsight

O Contrato de Nível de Serviço (SLA) é definido como uma _janela de suporte_. Uma janela de suporte é o período de tempo que uma versão do HDInsight tem suporte pelo Atendimento e Suporte ao Cliente da Microsoft. Se a versão tiver uma _data de término do suporte_ que já passou, o cluster HDInsight ficará fora da janela de suporte. A data de expiração do suporte de uma versão especificada do HDInsight (assim que uma versão X+1 mais nova estiver disponível) é a posterior entre:

- **Fórmula 1:** adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
- **Fórmula 2:** adicionar 90 dias à data em que o cluster HDInsight versão X+1 foi disponibilizado no portal do Azure.

A _data de baixa_ é a data após a qual a versão do cluster não pode ser criada no HDInsight. Desde 31 de julho de 2017, você não pode redimensionar um cluster HDInsight após a data de baixa dele.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tamanhos de máquina virtual e configuração de nó de padrão para clusters

Para obter mais informações sobre quais SKUs de máquina virtual selecionar para o cluster, confira [Detalhes de configuração de cluster do Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- [Configuração de cluster para o Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhe no Apache Hadoop no HDInsight a partir de um PC Windows](hdinsight-hadoop-windows-tools.md)
- [Notas sobre a versão do Hortonworks associadas a versões do Azure HDInsight](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)
