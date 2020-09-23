---
title: Publicar pipelines ML
titleSuffix: Azure Machine Learning
description: Execute fluxos de trabalho de Machine Learning com pipelines do Machine Learning e o SDK do Azure Machine Learning para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: ddc8186e85001a2a3ed2ed9f57b8f025133ef16a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897765"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicar e acompanhar pipelines do Machine Learning



Este artigo mostrará como compartilhar um pipeline de aprendizado de máquina com seus colegas ou clientes.

Pipelines de Machine Learning são fluxos de trabalho reutilizáveis para tarefas de aprendizado de máquina. Um benefício dos pipelines é o aumento da colaboração. Você também pode gerar pipelines de versão, permitindo que os clientes usem o modelo atual enquanto você estiver trabalhando em uma nova versão. 

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline

* [Configurar seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou usar uma [instância de computação Azure Machine Learning](concept-compute-instance.md) com o SDK já instalado

* Crie e execute um pipeline de Machine Learning, como o seguinte [tutorial: criar um pipeline de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md). Para outras opções, consulte [criar e executar pipelines do Machine Learning com o SDK do Azure Machine Learning](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Publicar um pipeline

Quando você tiver um pipeline em funcionamento, poderá publicar um pipeline para que ele seja executado com entradas diferentes. Para o ponto de extremidade REST de um pipeline já publicado para aceitar parâmetros, você deve configurar seu pipeline para usar `PipelineParameter` objetos para os argumentos que irão variar.

1. Para criar um parâmetro de pipeline, use um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) com um valor padrão.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione esse objeto `PipelineParameter` como um parâmetro a qualquer uma das etapas no pipeline da seguinte maneira:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este pipeline que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Execute um pipeline publicado

Todos os pipelines publicados têm um ponto de extremidade REST. Com o ponto de extremidade do pipeline, você pode disparar uma execução do pipeline de qualquer sistema externo, incluindo clientes não Python. Esse ponto de extremidade permite a "repetibilidade gerenciada" em cenários de retreinamento e pontuação de lote.

Para invocar a execução do pipeline anterior, você precisa de um token de cabeçalho de autenticação Azure Active Directory. Obter esse token é descrito na referência da [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) e na [autenticação no Azure Machine Learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto de extremidade de pipeline com versão

Você pode criar um ponto de extremidade de pipeline com vários pipelines publicados por trás dele. Isso lhe dá um ponto de extremidade REST fixo conforme você itera e atualiza seus pipelines de ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Enviar um trabalho para um ponto de extremidade de pipeline

Você pode enviar um trabalho para a versão padrão de um ponto de extremidade de pipeline:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Você também pode enviar um trabalho para uma versão específica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser feito usando a API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Usar pipelines publicados no estúdio

Você também pode executar um pipeline publicado no estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Exiba seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **pontos de extremidade**.

1. Na parte superior, selecione **pontos de extremidade do pipeline**.
 ![lista de pipelines publicados do Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um pipeline específico para executar, consumir ou examinar os resultados das execuções anteriores do ponto de extremidade do pipeline.

## <a name="disable-a-published-pipeline"></a>Desabilitar um pipeline publicado

Para ocultar um pipeline da lista de pipelines publicados, você o desabilita, no estúdio ou no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Você pode habilitá-lo novamente com `p.enable()` . Para obter mais informações, consulte referência de [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) .

## <a name="next-steps"></a>Próximas etapas

- Use [esses Jupyter Notebooks no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais pipelines de machine learning.
- Consulte a ajuda de referência do SDK para o pacote [azureml-pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) e o pacote [azureml-pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) .
- Consulte a [instruções](how-to-debug-pipelines.md) para obter dicas sobre como depurar e solucionar problemas de pipelines.
