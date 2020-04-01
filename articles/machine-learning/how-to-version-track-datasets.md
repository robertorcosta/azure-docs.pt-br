---
title: Versão do conjunto de dados
titleSuffix: Azure Machine Learning
description: Aprenda a melhor versão de seus conjuntos de dados e como a versão funciona com pipelines de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: 5bd4436fc63fb570f052606ab557dbcf243cf5e7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476863"
---
# <a name="version-and-track-datasets-in-experiments"></a>Versão e rastrear conjuntos de dados em experimentos
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá como versaver e rastrear os conjuntos de dados do Azure Machine Learning para reprodutibilidade. A versão do conjunto de dados é uma maneira de marcar o estado de seus dados para que você possa aplicar uma versão específica do conjunto de dados para experimentos futuros.

Cenários típicos de versionamento:

* Quando novos dados estão disponíveis para retreinamento
* Quando você está aplicando diferentes abordagens de preparação de dados ou de engenharia de recursos

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

- [Azure Machine Learning SDK para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Este SDK inclui o pacote [de conjuntos de dados azureml.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- Um [espaço de trabalho de aprendizado de máquina do Azure.](concept-workspace.md) Recupere um já existente executando o código a seguir ou [crie um novo espaço de trabalho](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Um [conjunto de dados de aprendizado de máquina do Azure](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registre e recupere versões do conjunto de dados

Ao registrar um conjunto de dados, você pode fazer a versão, reutilizá-lo e compartilhá-lo entre experimentos e com colegas. Você pode registrar vários conjuntos de dados com o mesmo nome e recuperar uma versão específica por nome e número de versão.

### <a name="register-a-dataset-version"></a>Registre uma versão do conjunto de dados

O código a seguir registra `titanic_ds` uma nova versão `create_new_version` do `True`conjunto de dados definindo o parâmetro para . Se não houver um `titanic_ds` conjunto de dados existente registrado no espaço de trabalho, `titanic_ds` o código criará um novo conjunto de dados com o nome e define sua versão como 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Recuperar um conjunto de dados pelo nome

Por padrão, o método [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na `Dataset` classe retorna a versão mais recente do conjunto de dados registrado no espaço de trabalho. 

O código a seguir `titanic_ds` recebe a versão 1 do conjunto de dados.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Versão da melhor prática

Quando você cria uma versão do conjunto de dados, você *não* está criando uma cópia extra de dados com o espaço de trabalho. Como os conjuntos de dados são referências aos dados do seu serviço de armazenamento, você tem uma única fonte de verdade, gerenciada pelo seu serviço de armazenamento.

>[!IMPORTANT]
> Se os dados referenciados pelo seu conjunto de dados forem substituídos ou excluídos, chamar uma versão específica do conjunto de dados *não* reverterá a alteração.

Quando você carrega dados de um conjunto de dados, o conteúdo de dados atual referenciado pelo conjunto de dados é sempre carregado. Se você quiser ter certeza de que cada versão do conjunto de dados é reprodutível, recomendamos que você não modifique o conteúdo de dados referenciado pela versão do conjunto de dados. Quando novos dados chegarem, salve novos arquivos de dados em uma pasta de dados separada e crie uma nova versão do conjunto de dados para incluir dados dessa nova pasta.

O seguinte código de imagem e amostra mostra a maneira recomendada de estruturar suas pastas de dados e criar versões de conjunto de dados que fazem referência a essas pastas:

![Estrutura de pastas](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Versão de um conjunto de dados de saída de pipeline

Você pode usar um conjunto de dados como a entrada e a saída de cada etapa do pipeline de Machine Learning. Quando você reexecuta pipelines, a saída de cada etapa do pipeline é registrada como uma nova versão do conjunto de dados.

Como os pipelines de Machine Learning preenchem a saída de cada etapa em uma nova pasta toda vez que o pipeline for reexecutado, os conjuntos de dados de saída versãodo são reprodutíveis. Saiba mais sobre [conjuntos de dados em pipelines](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Rastrear conjuntos de dados em experimentos

Para cada experimento de Aprendizado de Máquina, você pode facilmente `Run` rastrear os conjuntos de dados usados como entrada através do objeto do experimento.

O código a [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) seguir usa o método para rastrear quais conjuntos de dados de entrada foram usados com a execução do experimento:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Você também pode `input_datasets` encontrar a https://ml.azure.com/partir de experimentos usando . 

A imagem a seguir mostra onde encontrar o conjunto de dados de entrada de um experimento no estúdio Azure Machine Learning. Para este exemplo, vá ao painel **Experimentos** e abra a guia `keras-mnist` **Propriedades** para uma execução específica do seu experimento, .

![Conjuntos de dados de entrada](./media/how-to-version-track-datasets/input-datasets.png)

Use o seguinte código para registrar modelos com conjuntos de dados:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após o registro, você pode ver a lista de modelos https://ml.azure.com/registrados com o conjunto de dados usando Python ou ir para .

A seguinte visualização é do painel **Conjuntos de dados** em **Ativos**. Selecione o conjunto de dados e selecione a guia **Modelos** para uma lista dos modelos registrados no conjunto de dados. 

![Modelos de conjuntos de dados de entrada](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Próximas etapas

* [Treinar com conjuntos de dados](how-to-train-with-datasets.md)
* [Mais notebooks de conjunto de dados de exemplo](https://aka.ms/dataset-tutorial)
