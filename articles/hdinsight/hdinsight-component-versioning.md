---
title: Componentes e versões do Apache Hadoop - Azure HDInsight
description: Saiba mais sobre os componentes e as versões do Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124645"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Componentes e versões do Apache disponíveis para o Azure HDInsight

Neste artigo, você aprenderá sobre os componentes e as versões do ambiente de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight e o Enterprise Security Package. Você também aprenderá a verificar as versões de componente do Hadoop no HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Componentes do Apache disponíveis com diferentes versões do HDInsight

O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Em 4 de abril de 2017, a versão de cluster padrão usada pelo Azure HDInsight foi 3,6.

As versões do componente associadas às versões do cluster HDInsight são listadas na tabela a seguir.

> [!NOTE]
> A versão padrão para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar seus clusters com o SDK do .NET com Azure PowerShell e a CLI clássica do Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (padrão)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 na consulta interativa ESP) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| HBase no Apache           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| O Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| O Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (consulte a observação.)   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> O suporte para Kafka versão 0,10 expirou em 2019 de março devido a considerações de desempenho do sistema.

## <a name="check-for-current-apache-component-version-information"></a>Verificar informações atuais de versão do componente do Apache

As versões de componente do ambiente do Hadoop associadas às versões do cluster HDInsight podem ser alteradas com as atualizações do HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para obter mais informações, consulte a [documentação do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Notas de versão

Para obter notas de versão adicionais sobre as versões mais recentes do HDInsight, consulte [notas de versão do hdinsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Suporte à expiração e retirada para versões do HDInsight

**Expiração de suporte** significa que a Microsoft não fornece mais suporte para a versão específica do HDInsight. E não está mais disponível por meio do portal do Azure para a criação do cluster. Essas versões ainda podem ser criadas usando o CLI do Azure ou vários SDKs.

**Aposentadoria** significa que os clusters existentes de uma versão do HDInsight continuam a ser executados como estão. Novos clusters desta versão não podem ser criados por meio de nenhum meio, o que inclui a CLI e os SDKs. Outros recursos de plano de controle, como dimensionamento manual e dimensionamento automático, também podem não funcionar após a desativação da versão. O suporte não está disponível para versões desativadas.

As tabelas a seguir listam as versões do HDInsight. As datas de expiração e desativação do suporte também são fornecidas quando são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

Esta tabela lista as versões do HDInsight que estão disponíveis no portal do Azure e por meio de outros métodos de implantação, como o PowerShell e o SDK do .NET.

| Versão do HDInsight | SO da VM | Data de lançamento | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no portal do Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 31 de dezembro de 2020 |31 de dezembro de 2020 |Sim |Sim |

O suporte do Spark 2,1, 2,2 e Kafka 1,0 expira em 30 de junho de 2020.

> [!NOTE]
> Após o suporte para uma versão expirar, ela pode não estar disponível por meio do portal do Azure. As versões de cluster continuam disponíveis usando o parâmetro **version** no comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) do Windows PowerShell e o SDK do .net até a data de desativação da versão.

### <a name="retired-versions"></a>Versões desativadas

Esta tabela lista as versões do HDInsight que não estão disponíveis no portal do Azure.

| Versão do HDInsight | Versão do HDP | SO da VM | Data de lançamento | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no portal do Azure |
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
> Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight versão 2.1 e posterior. Eles não estão disponíveis para clusters do HDInsight versão 1,6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões de cluster HDInsight

O contrato de nível de serviço é definido como uma _janela de suporte_. Uma janela de suporte é o período de tempo em que uma versão do HDInsight é compatível com o serviço de suporte e atendimento ao cliente da Microsoft. Se a versão tiver passado a _data de validade do suporte_, o cluster HDInsight estará fora da janela de suporte. O vencimento do suporte para o HDInsight versão X (depois que uma versão mais recente do X + 1 estiver disponível) é posterior a:

- **Fórmula 1:** Adicione 180 dias à data em que o cluster HDInsight versão X foi liberado.
- **Fórmula 2:** Adicione 90 dias à data em que a versão X + 1 do cluster HDInsight é disponibilizada no portal do Azure.

A _data de aposentadoria_ é a data após a qual a versão do cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, você não pode redimensionar um cluster HDInsight após sua data de desativação.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tamanhos de máquina virtual e configuração de nó de padrão para clusters

Para obter mais informações sobre quais SKUs de máquina virtual selecionar para o cluster, consulte [detalhes de configuração do cluster HDInsight do Azure](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- [Configuração do cluster para Apache Hadoop, Spark e mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhar no Apache Hadoop no HDInsight em um computador Windows](hdinsight-hadoop-windows-tools.md)
- [Notas de versão do Hortonworks associadas às versões do Azure HDInsight](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)