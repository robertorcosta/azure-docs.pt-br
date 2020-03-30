---
title: Use registros do Monitor do Azure para monitorar os clusters do Azure HDInsight
description: Aprenda a usar os registros do Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162779"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Como usar logs do Azure Monitor para monitorar clusters do HDInsight

Saiba como ativar os logs do Azure Monitor para monitorar as operações de cluster Hadoop no HDInsight e como adicionar uma solução de monitoramento HDInsight.

[O Azure Monitor logs](../log-analytics/log-analytics-overview.md) é um serviço no Azure Monitor que monitora seus ambientes em nuvem e no local para manter sua disponibilidade e desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. Você pode pensar neste espaço de trabalho como um ambiente exclusivo de logs do Azure Monitor com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Um cluster Azure HDInsight. Atualmente, você pode usar logs do Monitor Do Azure com os seguintes tipos de cluster HDInsight:

  * O Hadoop
  * HBase
  * Consulta Interativa
  * Kafka
  * Spark
  * Storm

  Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Módulo Azure PowerShell Az.  Consulte [Apresentando o novo módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Certifique-se de ter a versão mais recente. Se necessário, `Update-Module -Name Az`corra.

> [!NOTE]  
> É recomendável colocar o cluster HDInsight e o espaço de trabalho do Log Analytics na mesma região para melhorar o desempenho. Os registros do Monitor do Azure não estão disponíveis em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Habilite os logs do Monitor do Azure usando o portal

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. No [portal Azure,](https://portal.azure.com/)selecione seu cluster.  Consulte [lista e mostrar clusters](./hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto em uma nova página do portal.

1. À esquerda, em **Monitoramento,** selecione **Monitor Azure**.

1. Na visão principal, em **Azure Monitor Integration,** selecione **Habilitar**.

1. Na lista suspensa **Selecionar espaço de trabalho**, selecione um espaço de trabalho do Log Analytics existente.

1. Selecione **Salvar**.  Levará alguns minutos para salvar a configuração.

    ![Habilite o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Habilite o monitoramento para clusters HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Habilite os logs do Monitor do Azure usando o Azure PowerShell

Você pode habilitar logs do Monitor Azure usando o módulo Azure PowerShell Az [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet.

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

Para desativar, use o [cmdlet Disable-AzHDInsightMonitoring:](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar solução de gerenciamento do cluster do Microsoft Azure HDInsight

O HDInsight fornece soluções de gerenciamento específicas de cluster que você pode adicionar para logs do Monitor Do Azure. [As soluções](../log-analytics/log-analytics-add-solutions.md) de gerenciamento adicionam funcionalidade aos logs do Azure Monitor, fornecendo ferramentas adicionais de dados e análises. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight e fornecem as ferramentas para pesquisá-las. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas.

Estas são as soluções de Microsoft Azure HDInsight disponíveis:

* Monitoramento do Hadoop no HDInsight
* Monitoramento do HDInsight HBase
* Monitoramento de Consulta Interativo do Microsoft Azure HDInsight
* Monitoramento do Kafka no HDInsight
* Monitoramento do Spark no HDInsight
* Monitoramento do Storm no HDInsight

Para obter instruções para instalar uma solução de gerenciamento, consulte [Soluções de Gerenciamento do Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale uma solução HDInsight Hadoop Monitoring. Quando estiver pronto, você verá um azulejo **HDInsightHadoop** listado em **Resumo**. Selecione a peça **HDInsightHadoop**. A solução HDInsightHadoop é semelhante a:

![Modo de exibição de solução de monitoramento do HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é uma marca nova, o relatório não mostra todas as atividades.

## <a name="configuring-performance-counters"></a>Configuração de contadores de desempenho

O monitor Azure também suporta a coleta e análise de métricas de desempenho para os nós em seu cluster. Para obter mais informações sobre como ativar e configurar esse recurso, consulte [as fontes de dados de desempenho do Linux no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de cluster

O HDInsight suporta auditoria de cluster com logs do Monitor Do Azure, importando os seguintes tipos de logs:

* `log_gateway_audit_CL`- esta tabela fornece registros de auditoria de nós de gateway de cluster que mostram tentativas de login bem sucedidas e fracassadas.
* `log_auth_CL`- esta tabela fornece logs SSH com tentativas de login bem sucedidas e fracassadas.
* `log_ambari_audit_CL`- esta tabela fornece registros de auditoria da Ambari.
* `log_ranger_audti_CL`- esta tabela fornece registros de auditoria do Apache Ranger em clusters ESP.

## <a name="next-steps"></a>Próximas etapas

* [Query Azure Monitor logs para monitorar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
