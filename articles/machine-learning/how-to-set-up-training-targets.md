---
title: Enviar uma execução de treinamento para um destino de computação
titleSuffix: Azure Machine Learning
description: Treine seu modelo de aprendizado de máquina em vários ambientes de treinamento (destinos de computação). É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente. Se precisar escalar horizontalmente, passe para um destino de computação em nuvem.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 8b07d19ca88a2d680a4f9efbb85fcf60b895a2b3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907590"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Enviar uma execução de treinamento para um destino de computação

Neste artigo, você aprenderá a usar vários ambientes de treinamento ([destinos de computação](concept-compute-target.md)) para treinar seu modelo de aprendizado de máquina.

Durante o treinamento, é comum iniciar em seu computador local e depois executar esse script de treinamento em um destino de computação diferentes. Com o Azure Machine Learning, você pode executar o script em vários destinos de computação sem precisar alterar o script de treinamento.

Tudo o que você precisa fazer é definir o ambiente para cada destino de computação em uma **configuração de execução de script**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para esse tipo de computação.

## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje
* O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Um destino de computação, `my_compute_target` .  Crie um destino de computação com:
  * [SDK do Python](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>O que é uma configuração de execução de script?

Você envia seu teste de treinamento com um objeto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) .  Esse objeto inclui:

* **source_directory**: o diretório de origem que contém o script de treinamento
* **script**: identificar o script de treinamento
* **run_config**: a [configuração de execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), que, por sua vez, define onde o treinamento ocorrerá. No `run_config` , você especifica o destino de computação e o ambiente a ser usado ao executar o script de treinamento.  

## <a name="whats-an-environment"></a>O que é um ambiente?

Os [ambientes](concept-environments.md) de Azure Machine Learning são um encapsulamento do ambiente no qual o treinamento do Machine Learning acontece. Eles especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker).  

Os ambientes são especificados no  `run_config` objeto dentro de um `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Treinar seu modelo

O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Criar um ambiente no qual o script será executado
1. Criar uma configuração de execução de script, que faz referência ao ambiente e ao destino de computação
1. Enviar a execução
1. Aguarde a conclusão da execução

Ou você pode:

* Enviar o teste com um objeto `Estimator` conforme mostrado em [Treinar modelos de ML com estimadores](how-to-train-ml-models.md).
* Envie uma execução de HyperDrive para [ajustar o hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie um experimento por meio da [extensão do VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento em seu espaço de trabalho.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Criar um ambiente

Os ambientes organizados contêm coleções de pacotes do Python e estão disponíveis em seu espaço de trabalho por padrão. Esses ambientes são apoiados por imagens do Docker armazenadas em cache, o que reduz o custo de preparação da execução. Para um destino de computação remota, você pode usar um desses ambientes estruturados populares para começar:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Para obter mais informações e detalhes sobre ambientes, consulte [criar & usar ambientes de software no Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Destino de computação local

Se o seu destino de computação for seu **computador local**, você será responsável por garantir que todos os pacotes necessários estejam disponíveis no ambiente do Python em que o script é executado.  Use o `python.user_managed_dependencies` para usar seu ambiente atual do Python (ou o Python no caminho que você especificar).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Criar configuração de execução de script

Agora que você tem um destino de computação ( `compute_target` ) e um ambiente ( `my_environment` ), crie uma configuração de execução de script que execute seu script de treinamento ( `train.py` ) localizado em seu `project_folder` diretório:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Talvez você também queira definir a estrutura para sua execução.

* Para um cluster HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Para uma máquina virtual remota:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Enviar o teste

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Ao enviar a execução de treinamento, um instantâneo do diretório contendo os scripts de treinamento será criado e enviado ao destino de computação. Ele também é armazenado como parte do experimento em seu workspace. Se você alterar os arquivos e enviar a execução novamente, apenas os arquivos alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para obter mais informações sobre instantâneos, consulte [instantâneos](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Acompanhamento e integração do Git

Quando você inicia uma execução de treinamento em que o diretório de origem é um repositório Git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, confira [Integração do Git com o Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar os hiperparâmetros com eficiência](how-to-tune-hyperparameters.md) para criar modelos melhores.? View = Azure-ml-py&preserve-View = true)
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SKD da [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true).
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](how-to-enable-virtual-network.md)