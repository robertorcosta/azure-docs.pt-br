---
title: Práticas recomendadas de gerenciamento de cluster - Azure HDInsight
description: Aprenda as melhores práticas para gerenciar clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782015"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Práticas recomendadas de gerenciamento de cluster hdInsight

Aprenda as melhores práticas para gerenciar clusters HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Como crio clusters HDInsight?

| Opção | Documentos |
|---|---|
| Fábrica de dados do Azure | [Criar clusters do Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Modelo de gerenciador de recursos personalizado | [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Modelos de início rápido | [Modelos hdinsight quickstart](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Amostras do Azure | [Amostras do HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portal do Azure | [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| CLI do Azure | [Criar os clusters do HDInsight usando a CLI do Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Criar clusters do Apache Hadoop usando a API REST do Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Se você estiver criando um cluster e reutilizando o nome do cluster de um cluster criado anteriormente, aguarde até que a exclusão anterior do cluster seja concluída antes de criar o cluster.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Como personalizo clusters HDInsight?

| Opção | Documentos |
|---|---|
| Ações de script | [Personalize os clusters Do Azure HDInsight usando ações de script](./hdinsight-hadoop-customize-cluster-linux.md) |
| Inicialização | [Personalizar clusters do HDInsight usando a Inicialização](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Metalojas externas | [Usar armazenamentos de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| BD Ambari personalizado | [Configure clusters HDInsight com um DB Ambari personalizado](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Quais são alguns erros que eu posso enfrentar ao criar clusters?

| Erro | Mais informações |
|---|---|
| Sem cota | Existem cotas para o número de cotas que você pode criar em sua assinatura em cada região. Para obter mais informações, consulte [Planejamento de capacidade: cotas](./hdinsight-capacity-planning.md). |
| Não há mais endereços IP disponíveis | Cada VNet tem um número limitado de endereços IP. Quando você cria um cluster HDInsight, cada nó (incluindo nós de zookeeper e gateway) usa alguns desses endereços IP alocados. Quando todos os endereços IP estiverem em uso, você encontrará esse erro.  |
| As regras do Grupo de Segurança de Rede (NSG) não permitem a comunicação com provedores de recursos HDInsight | Se você usar NSGs ou UDRs (UDRs) definidos pelo usuário para controlar o tráfego de entrada no seu cluster HDInsight, você deve garantir que seu cluster possa se comunicar com serviços críticos de saúde e gerenciamento do Azure. Para obter mais informações, consulte [tags de serviço do Grupo de Segurança de Rede (NSG) para o Azure HDInsight](./hdinsight-service-tags.md) |
| Reutilização do nome do cluster | Quando você usa um nome de cluster que você já usou antes, você precisa esperar x número de minutos antes de recriar o cluster. Caso contrário, você verá uma mensagem de que o recurso já existe. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Como faço para gerenciar a execução de clusters HDInsight?

| Opção | Documentos |
|---|---|
| Autoscale | [Dimensionar automaticamente os clusters do Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Dimensionamento manual | [Scale Azure HDInsight clusters](./hdinsight-scaling-best-practices.md) |
| Monitoramento com Ambari| [Monitore o desempenho do cluster no Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitoramento com registros do Monitor do Azure | [Como usar logs do Azure Monitor para monitorar clusters do HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Como faço para verificar em clusters HDInsight excluídos?

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Você pode usar a seguinte consulta com os logs do Monitor do Azure para monitorar clusters excluídos.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Próximas etapas

* [Planejamento de capacidade para clusters do HDInsight](./hdinsight-capacity-planning.md)
* [Quais são as configurações padrão e recomendadas de nó para o Azure HDInsight?](./hdinsight-supported-node-configuration.md)