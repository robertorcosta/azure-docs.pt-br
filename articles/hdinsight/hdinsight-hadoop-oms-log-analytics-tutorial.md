---
title: Usar logs de Azure Monitor para monitorar os clusters do Azure HDInsight
description: Saiba como usar os logs de Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 41688792330214943eeb116dc4b5aaf7eebfeebf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192035"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Como usar logs do Azure Monitor para monitorar clusters do HDInsight

Saiba como habilitar logs de Azure Monitor para monitorar as operações de cluster do Hadoop no HDInsight. E como adicionar uma solução de monitoramento do HDInsight.

[Os logs de Azure monitor](../log-analytics/log-analytics-overview.md) são um serviço de Azure monitor que monitora seus ambientes locais e de nuvem. O monitoramento é manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em sua nuvem, ambientes locais e de outras ferramentas de monitoramento. Os dados são usados para fornecer análise em várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. Você pode considerar esse espaço de trabalho como um ambiente de logs de Azure Monitor exclusivo com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Um cluster Azure HDInsight. No momento, você pode usar os logs de Azure Monitor com os seguintes tipos de cluster HDInsight:

  * O Hadoop
  * HBase
  * Consulta Interativa
  * Kafka
  * Spark
  * Storm

  Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell módulo AZ.  Consulte [apresentando o novo módulo Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Verifique se você tem a versão mais recente. Se necessário, execute `Update-Module -Name Az`.

> [!NOTE]  
> É recomendável colocar o cluster HDInsight e o espaço de trabalho do Log Analytics na mesma região para melhorar o desempenho. Os logs de Azure Monitor não estão disponíveis em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Habilitar logs de Azure Monitor usando o portal

Nesta seção, você configura um cluster Hadoop do HDInsight existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração e assim por diante.

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster. O cluster é aberto em uma nova página do Portal.

1. À esquerda, em **monitoramento**, selecione **Azure monitor**.

1. Na exibição principal, em **integração de Azure monitor**, selecione **habilitar**.

1. Na lista suspensa **Selecionar espaço de trabalho**, selecione um espaço de trabalho do Log Analytics existente.

1. Clique em **Salvar**.  Levará alguns minutos para salvar a configuração.

    ![Habilitar o monitoramento de clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Habilitar o monitoramento de clusters HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Habilitar logs de Azure Monitor usando Azure PowerShell

Você pode habilitar os logs de Azure Monitor usando o cmdlet [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) do módulo Azure PowerShell AZ.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Para desabilitar, use o cmdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar solução de gerenciamento do cluster do Microsoft Azure HDInsight

O HDInsight fornece soluções de gerenciamento específicas de cluster que você pode adicionar para logs de Azure Monitor. As [soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) adicionam funcionalidade a logs de Azure monitor, fornecendo dados adicionais e ferramentas de análise. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight. E forneça as ferramentas para pesquisar as métricas. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas.

Soluções do HDInsight disponíveis:

* Monitoramento do Hadoop no HDInsight
* Monitoramento do HDInsight HBase
* Monitoramento de Consulta Interativo do Microsoft Azure HDInsight
* Monitoramento do Kafka no HDInsight
* Monitoramento do Spark no HDInsight
* Monitoramento do Storm no HDInsight

Para obter instruções de solução de gerenciamento, consulte [soluções de gerenciamento no Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale uma solução de monitoramento do Hadoop do HDInsight. Quando terminar, você verá um bloco **HDInsightHadoop** listado em **Resumo**. Selecione a peça **HDInsightHadoop**. A solução HDInsightHadoop é semelhante a:

![Modo de exibição de solução de monitoramento do HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é uma marca nova, o relatório não mostra todas as atividades.

## <a name="configuring-performance-counters"></a>Configurando contadores de desempenho

O Azure monitor dá suporte à coleta e análise de métricas de desempenho para os nós no cluster. Para obter mais informações, consulte [fontes de dados de desempenho do Linux no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de cluster

O HDInsight dá suporte à auditoria de cluster com logs de Azure Monitor, importando os seguintes tipos de logs:

* `log_gateway_audit_CL`-Esta tabela fornece logs de auditoria de nós de gateway de cluster que mostram tentativas de logon bem-sucedidas e com falha.
* `log_auth_CL`-Esta tabela fornece logs SSH com tentativas de logon bem-sucedidas e com falha.
* `log_ambari_audit_CL`-Esta tabela fornece logs de auditoria do Ambari.
* `log_ranger_audti_CL`-Esta tabela fornece logs de auditoria do Apache Ranger em clusters ESP.

## <a name="next-steps"></a>Próximas etapas

* [Consultar logs de Azure Monitor para monitorar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Como monitorar a disponibilidade do cluster com os logs do Apache Ambari e Azure Monitor](./hdinsight-cluster-availability.md)
