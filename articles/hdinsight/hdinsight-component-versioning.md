---
title: Componentes e versões do Apache Hadoop - Azure HDInsight
description: Conheça os componentes e as versões do Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/09/2020
ms.openlocfilehash: 87c3e2439d1b4bef4a58663e3ea06d8bb7cb9b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192528"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?

Saiba mais sobre os componentes e as versões do ambiente de [Apache Hadoop](https://hadoop.apache.org/) no Microsoft Azure HDInsight e o Enterprise Security Package. Além disso, saiba como verificar as versões dos componentes do Hadoop no HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componentes do Apache Hadoop disponíveis com diferentes versões do HDInsight

O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Em 4 de abril de 2017, a versão de cluster padrão usada pelo Azure HDInsight é 3,6.

As versões do componente associadas às versões do cluster HDInsight são listadas na tabela a seguir:

> [!NOTE]  
> A versão padrão para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar clusters com o SDK do .NET com o Azure PowerShell e a CLI Clássica do Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (Padrão)     |
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
| Apache Kafka           | 1.1.1, 2,1    | 1.1, 1.0 * (Veja a Observação abaixo) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Devido às considerações de desempenho do sistema, o suporte ao Kafka versão 0.10 expirou em março de 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Verificar informações atuais de versão do componente do Hadoop

As versões de componente do ambiente do Hadoop associadas às versões do cluster HDInsight podem ser alteradas com as atualizações do HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para detalhes, veja a documentação do [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Notas de versão

Consulte [Notas de versão do HDInsight](hdinsight-release-notes.md) para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Suporte à expiração e retirada para versões do HDInsight

**Expiração de suporte** significa que a Microsoft não fornecerá mais suporte para a versão especificada do HDInsight. E ele não estará mais disponível por meio do portal do Azure para a criação do cluster. No entanto, essas versões ainda podem ser criadas usando o CLI do Azure ou vários SDKs.

A desativação **de uma** versão do HDInsight significa que os clusters existentes continuarão a ser executados no estado em que se encontram. No entanto, novos clusters dessa versão não podem ser criados por meio de nenhum meio (incluindo CLI e SDKs). Outros recursos de plano de controle (como dimensionamento manual e dimensionamento automático) também podem não funcionar após a desativação da versão. O suporte não está disponível para versões desativadas.

As tabelas a seguir listam as versões do HDInsight. As datas de expiração e de desativação de suporte também são fornecidas, quando elas são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

A tabela a seguir lista as versões do HDInsight que estão disponíveis no portal do Azure e outros métodos de implantação, como o PowerShell e o SDK do .NET.

| Versão do HDInsight | SO da VM | Data de liberação | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no portal do Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 31 de dezembro de 2020 |31 de dezembro de 2020 |Sim |Sim |

O Spark 2,1, 2,2 & o suporte a 1,0 Kafka irá expirar em 30 de junho de 2020.

> [!NOTE]  
> Depois que o suporte para uma versão tiver expirado, ela poderá não estar disponível por meio do Portal do Microsoft Azure. No entanto, as versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando do Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e o SDK .NET até a data de baixa da versão.

### <a name="retired-versions"></a>Versões desativadas

A tabela a seguir lista as versões do HDInsight que **não estão** disponíveis no portal do Azure.

| Versão do HDInsight | Versão do HDP | SO da VM | Data de liberação | Data de expiração do suporte | Data de baixa | Alta disponibilidade |  Disponibilidade no Portal do Azure |
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
> Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight versão 2.1 e posterior. Eles não estão disponíveis para clusters HDInsight versão 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Pacote de Segurança Enterprise para HDInsight

O Enterprise Security é um pacote opcional que pode ser adicionado ao cluster HDInsight como parte do fluxo de trabalho de criação de cluster. O Pacote de Segurança Enterprise oferece suporte a:

- Integração com o Active Directory para autenticação.

    No passado, você criou clusters HDInsight com o usuário administrador local e o usuário SSH local. O usuário administrador local podia acessar todos os arquivos, pastas, tabelas e colunas.  Com o Enterprise Security Package, você habilita o controle de acesso baseado em função integrando o HDInsight ao seu Active Directory. Que inclui Active Directory local, Azure Active Directory Domain Services. Ou Active Directory na máquina virtual IaaS. O administrador de domínio no cluster pode conceder aos usuários o uso de seu próprio nome de usuário corporativo (domínio) e senha.

    Para obter mais informações, consulte:

    - [Uma introdução à segurança do Apache Hadoop com clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md)
    - [Planejar clusters do Apache Hadoop ingressados no domínio do Azure no HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurar ambiente de área restrita ingressado no domínio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorização pra dados

  - Integração com o Apache Ranger para autorização de Hive, Spark SQL e filas de Yarn.
  - Você pode configurar o controle de acesso em arquivos e pastas.

    Para obter mais informações, consulte:

  - [Configurar políticas do Apache Hive no HDInsight ingressado no domínio](./domain-joined/apache-domain-joined-run-hive.md)

- Exiba os logs de auditoria para monitorar acessos e as políticas configuradas.

### <a name="supported-cluster-types"></a>Tipos de cluster com suporte

Atualmente, apenas os tipos de cluster a seguir oferecem suporte ao Pacote de Segurança Enterprise:

- Hadoop (somente HDInsight 3.6)
- Spark
- Kafka
- HBase
- Consulta Interativa

### <a name="support-for-azure-data-lake-storage"></a>Suporte ao Azure Data Lake Storage

O Enterprise Security Package oferece suporte ao uso do Azure Data Lake Storage como o armazenamento primário e o armazenamento de complemento.

### <a name="pricing-and-service-level-agreement-sla"></a>Preço e contrato de nível de serviço (SLA)

Para obter informações sobre preços e SLA para o Pacote de Segurança Enterprise, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões do cluster HDInsight

O SLA (contrato de nível de serviço) é definido como uma _janela de suporte_. A janela de suporte é o período de tempo pelo `Microsoft Customer Service and Support`qual uma versão do HDInsight é suportada. Se a versão tiver uma _data de expiração de suporte_passada, o cluster HDInsight estará fora da janela de suporte. O vencimento do suporte para o HDInsight versão X (depois que uma versão mais recente do X + 1 estiver disponível) é o mais recente:  

- Fórmula 1: adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
- Fórmula 2: adicionar 90 dias à data em que o cluster HDInsight versão X+1 foi disponibilizado no Portal do Azure.

A _data de aposentadoria_ é a data após a qual a versão do cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, não é possível redimensionar um cluster HDInsight após sua data de desativação.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tamanhos de máquina virtual e configuração de nó de padrão para clusters

Para obter mais informações sobre quais SKUs de máquina virtual selecionar para o cluster, consulte [detalhes de configuração do cluster HDInsight do Azure](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- [Configuração do cluster para Apache Hadoop, Spark e mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhar no Apache Hadoop no HDInsight em um computador Windows](hdinsight-hadoop-windows-tools.md)
- [Notas de versão do Hortonworks associadas às versões do Azure HDInsight](./hortonworks-release-notes.md)
