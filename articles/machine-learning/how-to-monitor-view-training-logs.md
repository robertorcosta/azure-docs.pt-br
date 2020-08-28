---
title: Monitorar e exibir logs de execução do ML & métricas
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML e exiba as métricas de execução para aprimorar o processo de criação de modelo. Use widgets e o portal do estúdio para explorar o status de execução e exibir os registros de execução.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d06186b2ce4d8bb9143663d41f03b9508e4bd00e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005948"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorar e exibir os logs e as métricas de execução de ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a monitorar as execuções de Azure Machine Learning e exibir seus logs. Antes de poder exibir os logs, você precisa habilitá-los primeiro. Para obter mais informações, consulte [habilitar o registro em log nas execuções de treinamento do Azure ml](how-to-track-experiments.md).

Os logs podem ajudá-lo a diagnosticar erros e avisos ou rastrear métricas de desempenho como parâmetros e precisão do modelo. Neste artigo, você aprenderá a exibir os logs usando os seguintes métodos:

> [!div class="checklist"]
> * Monitorar execuções no estúdio
> * Monitorar execuções usando o widget Jupyter Notebook
> * Monitorar execuções automatizadas do Machine Learning
> * Exibir logs de saída após a conclusão
> * Exibir logs de saída no estúdio

Para obter informações gerais sobre como gerenciar seus experimentos, consulte [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>Monitorar execuções no estúdio

Para monitorar execuções para um destino de computação específico do seu navegador, use as seguintes etapas:

1. No [Azure Machine Learning Studio](https://ml.azure.com/), selecione seu espaço de trabalho e, em seguida, selecione __computação__ no lado esquerdo da página.

1. Selecione __Clusters de Treinamento__ para exibir uma lista de destinos de computação usados para treinamento. Em seguida, selecione o cluster.

    ![Selecionar o cluster de treinamento](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Execuções__. A lista de execuções que usam esse cluster é exibida. Para exibir detalhes de uma execução específica, use o link na coluna __Executar__. Para exibir detalhes do experimento, use o link na coluna __Experimento__.

    ![Selecionar execuções do cluster de treinamento](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Como os destinos de computação de treinamento são um recurso compartilhado, eles podem ter várias execuções enfileiradas ou ativas em um determinado momento.
    > 
    > Uma execução pode conter execuções filhas. Portanto, um trabalho de treinamento pode resultar em várias entradas.

Quando uma execução é concluída, ela deixa de ser exibida nessa página. Para exibir informações sobre execuções concluídas, visite a seção __Experimentos__ do estúdio e selecione o experimento e a execução. Para obter mais informações, consulte a seção [Exibir métricas para execuções concluídas](#view-the-experiment-in-the-web-portal).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorar execuções usando o widget Jupyter Notebook

Ao usar o método **ScriptRunConfig** para enviar execuções, você pode observar o progresso da execução usando o [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho seja concluído.

Exiba o widget do Jupyter enquanto aguarda a execução ser concluída.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Captura de tela do widget do Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Você também pode obter um link para a mesma exibição no seu workspace.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitorar execuções automatizadas do Machine Learning

Para execuções automatizadas do Machine Learning, para acessar os gráficos de uma execução anterior, substitua `<<experiment_name>>` pelo nome de teste apropriado:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget do notebook Jupyter para Machine Learning automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Mostrar saída após a conclusão

Quando você usa **ScriptRunConfig**, pode usar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treinamento do modelo está concluído. O sinalizador ```show_output``` fornece saída detalhada. Para obter mais informações, consulte a seção ScriptRunConfig de [como habilitar o registro em log](how-to-track-experiments.md#scriptrunconfig-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Métricas de execução de consulta

Você pode exibir as métricas de um modelo treinado usando ```run.get_metrics()```. Por exemplo, você pode usar isso com o exemplo acima para determinar o melhor modelo procurando o modelo com o valor do MSE (erro de quadrado médio) mais baixo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Exibir registros de execução no estúdio

Você pode procurar os registros de execução concluídos, incluindo as métricas registradas, no [Azure Machine Learning Studio](https://ml.azure.com).

Navegue até a guia **experimentos** e selecione seu experimento. No painel Executar teste, você pode ver as métricas e os logs acompanhados para cada execução. 

Faça uma busca detalhada até uma execução específica para exibir suas saídas ou logs ou baixe o instantâneo do experimento para que você possa compartilhar a pasta experimento com outras pessoas.

Você também pode editar a tabela de lista de execução para selecionar várias execuções e exibir o valor registrado por último, mínimo ou máximo para suas execuções. Personalize seus gráficos para comparar os valores de métricas registrados e as agregações entre várias execuções.

![Detalhes da execução no estúdio do Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Formatar gráficos no estúdio

Use os métodos a seguir nas APIs de registro para influenciar que o estúdio visualize suas métricas.

|Valor conectado|Código de exemplo| Formato no portal|
|----|----|----|
|Registrar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável único|
|Registre um único valor numérico com o mesmo nome de métrica usado repetidamente (como em um loop for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linhas de variável-único|
|Faça uma linha com colunas numéricas 2 repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de log com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|


## <a name="next-steps"></a>Próximas etapas

Experimente estas próximas etapas para aprender a usar o Azure Machine Learning:

* Saiba como [acompanhar experimentos e habilitar logs no designer de Azure Machine Learning (versão prévia)](how-to-track-designer-experiments.md).

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

