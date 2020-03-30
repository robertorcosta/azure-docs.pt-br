---
title: Monitoramento do Aprendizado de Máquina do Azure | Microsoft Docs
description: Aprenda a usar o Azure Monitor para visualizar, analisar e criar alertas em métricas do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399105"
---
# <a name="monitoring-azure-machine-learning"></a>Monitoramento do Aprendizado de Máquina do Azure

Este artigo descreve os dados de monitoramento gerados pelo Azure Machine Learning. Ele também descreve como você pode usar o Monitor do Azure para analisar seus dados e definir alertas.

> [!TIP]
> As informações neste documento são principalmente para administradores, pois descreve o monitoramento para o Azure Machine Learning. Se você é um cientista de dados ou desenvolvedor, e deseja monitorar informações específicas para o seu modelo de treinamento executado, consulte os seguintes documentos:
>
> * [Inicie, monitore e cancele as corridas de treinamento](how-to-manage-runs.md)
> * [Métricas de logs para execuções de treinamento](how-to-track-experiments.md)
> * [Acompanhar experimentos com o MLflow](how-to-use-mlflow.md)
> * [Visualizar execuções com o TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

O Azure Machine Learning registra dados de monitoramento usando o Azure Monitor, que é um serviço de monitoramento de pilha completa no Azure. O Azure Monitor fornece um conjunto completo de recursos para monitorar seus recursos do Azure. Também pode monitorar recursos em outras nuvens e no local.

Comece com a visão geral do [artigo Azure Monitor,](/azure/azure-monitor/overview)que fornece uma visão geral dos recursos de monitoramento. As seções a seguir se baseiam nestas informações fornecendo detalhes do uso do Monitor Azure com o Azure Machine Learning.

Para entender os custos associados ao Azure Monitor, consulte [Uso e custos estimados](/azure/azure-monitor/platform/usage-estimated-costs). Para entender o tempo que os seus dados levam para aparecer no Azure Monitor, consulte [O tempo de ingestão de dados do Log](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitoramento de dados do Azure Machine Learning

O Azure Machine Learning coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos no [Monitoramento de dados dos recursos do Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Consulte a referência de dados de monitoramento do [Azure Machine Learning](monitor-resource-reference.md) para obter uma referência detalhada dos logs e métricas criados pelo Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analisando dados métricos

Você pode analisar métricas para O Zure Machine Learning abrindo **Métricas** do menu **Do Monitor do Azure.** Consulte [Como começar com o Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre o uso desta ferramenta.

Todas as métricas para Azure Machine Learning estão no espaço de nome **Machine Learning Service Workspace**.

![Metrics Explorer com espaço de trabalho de serviço de aprendizagem de máquina selecionado](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrando e dividindo

Para métricas que suportam dimensões, você pode aplicar filtros usando um valor de dimensão. Por exemplo, filtrar **cores ativos** `cpu-cluster`para um nome de **cluster** de . 

Você também pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam entre si. Por exemplo, dividindo o **Pipeline Step Type** para ver uma contagem dos tipos de etapas usadas no pipeline.

Para obter mais informações sobre filtragem e divisão, consulte [Recursos avançados do Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alertas

Você pode acessar alertas para o Azure Machine Learning abrindo **alertas** do menu **do Monitor Do Azure.** Consulte [Criar, visualizar e gerenciar alertas métricos usando o Azure Monitor](/azure/azure-monitor/platform/alerts-metric) para obter detalhes sobre a criação de alertas.

A tabela a seguir lista regras de alerta métricacomuns e recomendadas para o Azure Machine Learning:

| Tipo de alerta | Condição | Descrição |
|:---|:---|:---|
| Falha na implantação do modelo | Tipo de agregação: Total, Operador: Maior que, Valor limite: 0 | Quando uma ou mais implantações de modelos falharam |
| Percentual de Utilização de Cotas | Tipo de agregação: Médio, Operador: Maior que, Valor limite: 90| Quando o percentual de utilização do contingente for superior a 90% |
| Nós inutilizáveis | Tipo de agregação: Total, Operador: Maior que, Valor limite: 0 | Quando há um ou mais nós inutilizáveis |

## <a name="configuration"></a>Configuração

> [!IMPORTANT]
> __As métricas para aprendizado de máquina do Azure não precisam ser configuradas, elas__são coletadas automaticamente e estão disponíveis no Metrics Explorer para monitoramento e alerta.

Você pode adicionar uma configuração de diagnóstico para configurar a seguinte funcionalidade:

* Arquivar informações de registro e métricas para uma conta de armazenamento do Azure.
* Transmita informações de log e métricas para um Azure Event Hub.
* Envie informações de log e métricas para o Azure Monitor Log Analytics.

A habilitação dessas configurações requer serviços adicionais do Azure (conta de armazenamento, hub de eventos ou Análise de Log), o que pode aumentar seu custo. Para calcular um custo estimado, visite a [calculadora de preços do Azure.](https://azure.microsoft.com/pricing/calculator)

Para obter mais informações sobre a criação de uma configuração de diagnóstico, consulte [Criar configuração de diagnóstico para coletar registros e métricas da plataforma no Azure](/azure/azure-monitor/platform/diagnostic-settings).

Você pode configurar os seguintes logs para Aprendizado de Máquina do Azure:

| Categoria | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters de computação de aprendizado de máquina do Azure. |
| AmlComputeClusterNodeEvent | Eventos de nódulos dentro de um cluster de computação de aprendizado de máquina do Azure. |
| AmlComputeJobEvent | Eventos de trabalhos em execução no azure Machine Learning compute. |

> [!NOTE]
> Quando você habilita métricas em uma configuração de diagnóstico, as informações de dimensão não são incluídas no momento como parte das informações enviadas para uma conta de armazenamento, hub de eventos ou análise de log.

## <a name="analyzing-log-data"></a>Analisando dados de log

O uso do Azure Monitor Log Analytics exige que você crie uma configuração de diagnóstico e __habilite enviar informações ao Log Analytics__. Para obter mais informações, consulte a seção [Configuração.](#configuration)

Os dados no Azure Monitor Logs são armazenados em tabelas, com cada tabela tendo seu próprio conjunto de propriedades únicas. O Azure Machine Learning armazena dados nas tabelas a seguir:

| Tabela | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters de computação de aprendizado de máquina do Azure. |
| AmlComputeClusterNodeEvent | Eventos de nódulos dentro de um cluster de computação de aprendizado de máquina do Azure. |
| AmlComputeJobEvent | Eventos de trabalhos em execução no azure Machine Learning compute. |

> [!IMPORTANT]
> Quando você **seleciona Logs** no menu Azure Machine Learning, o Log Analytics é aberto com o escopo de consulta definido para o espaço de trabalho atual. Isso significa que as consultas de log incluirão apenas dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou dados de outros serviços do Azure, **selecione Logs** no menu Do Monitor do **Azure.** Consulte [o escopo de consulta de log e o intervalo de tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter detalhes.

Para obter uma referência detalhada dos registros e métricas, consulte a referência de dados de [monitoramento do Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-queries"></a>Consultas de exemplo

A seguir estão as consultas que você pode usar para ajudá-lo a monitorar seus recursos de Machine Learning do Azure: 

+ Conseguir empregos fracassados nos últimos cinco dias:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obter registros para um nome de trabalho específico:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obtenha eventos de cluster nos últimos cinco dias para clusters onde o tamanho da VM é Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Obter nódulos alocados nos últimos oito dias:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência dos logs e métricas, consulte a referência de dados de [monitoramento do Azure Machine Learning](monitor-resource-reference.md).
- Para obter informações sobre como trabalhar com cotas relacionadas ao Azure Machine Learning, consulte [Gerenciar e solicitar cotas para recursos do Azure](how-to-manage-quotas.md).
- Para obter detalhes sobre o monitoramento dos recursos do Azure, consulte [Monitorando os recursos do Azure com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
