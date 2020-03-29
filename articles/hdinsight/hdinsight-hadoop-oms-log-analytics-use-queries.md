---
title: Query Azure Monitor logs para monitorar clusters Azure HDInsight
description: Saiba como executar consultas nos registros do Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803760"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Query Azure Monitor logs para monitorar clusters HDInsight

Conheça alguns cenários básicos sobre como usar os logs do Azure Monitor para monitorar os clusters do Azure HDInsight:

* [Analisar métricas de cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter configurado um cluster HDInsight para usar logs do Monitor do Azure e adicionado as soluções de monitoramento de logs do Azure Monitor específicas do cluster HDInsight para o espaço de trabalho. Para obter instruções, consulte [Use registros do Monitor Azure com clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas de cluster HDInsight

Saiba como procurar métricas específicas para seu cluster HDInsight.

1. Abra o espaço de trabalho do Log Analytics que está associado ao seu cluster HDInsight do portal do Azure.
1. Em **Geral,** **selecione Logs**.
1. Digite a seguinte consulta na caixa de pesquisa para procurar todas as métricas disponíveis para todos os clusters HDInsight configurados para usar os logs do Monitor do Azure e, em seguida, selecione **Executar**. Revise os resultados.

    ```kusto
    search *
    ```

    ![Análises do Apache Ambari pesquisam todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquise todas as métricas")

1. No menu esquerdo, selecione a guia **Filtro.**

1. Em **Tipo,** selecione **Heartbeat**. Em seguida, **selecione Aplicar & Executar**.

    ![pesquisa de análise de log métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Busca por métricas específicas")

1. Observe que a consulta na caixa de texto é alterado para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Você pode cavar mais fundo usando as opções disponíveis no menu esquerdo. Por exemplo: 

    - Para ver logs de um nó específico:

        ![Procure por saída de erros específicos1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Procure por saída de erros específicos1")

    - Para ver logs em certos momentos:

        ![Procure por saída de erros específicos2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Procure por saída de erros específicos2")

1. Selecione **Aplicar & Executar** e revisar os resultados. Observe também que a consulta foi atualizada para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Consultas adicionais de amostra

Uma consulta de amostra com base na média dos recursos utilizados em um intervalo de 10 minutos, categorizada pelo nome do cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Em vez de refinar com base na média de recursos usados, é possível usar a seguinte consulta para refinar os resultados com base em quando os recursos máximos foram usados (assim como o 90º e o 95º percentil) em uma janela de 10 minutos:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreamento

A primeira etapa para criar um alerta é chegar a uma consulta com base na qual o alerta é disparado. É possível usar qualquer consulta que você desejar para criar um alerta.

1. Abra o espaço de trabalho do Log Analytics que está associado ao seu cluster HDInsight do portal do Azure.
1. Em **Geral,** **selecione Logs**.
1. Execute a seguinte consulta na qual deseja criar um alerta e, em seguida, **selecione Executar**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A consulta fornece uma lista de aplicativos com falha em execução em clusters de HDInsight.

1. Selecione **Nova regra de alerta** no topo da página.

    ![Digite consulta para criar um alerta1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Digite consulta para criar um alerta1")

1. Na janela **Criar regra**, digite a consulta e outros detalhes para criar um alerta e, em seguida, selecione **Criar regra de alerta**.

    ![Digite consulta para criar um alerta2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Digite consulta para criar um alerta2")

### <a name="edit-or-delete-an-existing-alert"></a>Editar ou excluir um alerta existente

1. Abra o espaço de trabalho do Log Analytics no portal do Azure.

1. No menu esquerdo, em **Monitoramento,** selecione **Alertas**.

1. Para o topo, selecione **Gerenciar regras de alerta**.

1. Selecione o alerta que você deseja editar ou excluir.

1. Você tem as seguintes opções: **Salvar**, **Descartar**, **Desabilitar**, e **Excluir**.

    ![HDInsight Azure Monitor logs alerta excluir edição](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Confira também

* [Introdução às consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Crie visualizações personalizadas usando o View Designer no Monitor Azure](../azure-monitor/platform/view-designer.md)
