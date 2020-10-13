---
title: Treinar um modelo usando uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Saiba como treinar modelos com imagens personalizadas do Docker no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8239d037d6bd68638998cbb36c47c7dac4bce30d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537609"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Treinar um modelo usando uma imagem personalizada do Docker

Neste artigo, saiba como usar uma imagem personalizada do Docker ao treinar modelos com Azure Machine Learning. 

Os scripts de exemplo neste artigo são usados para classificar imagens de animais de estimação criando uma rede neural de convolução. 

Embora Azure Machine Learning forneça uma imagem base padrão do Docker, você também pode usar ambientes de Azure Machine Learning para especificar uma imagem base específica, como um conjunto de imagens de [base de ml do Azure](https://github.com/Azure/AzureML-Containers) mantidas ou sua própria [imagem personalizada](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). As imagens de base personalizadas permitem que você gerencie de perto suas dependências e mantenha um controle mais rígido sobre as versões de componentes ao executar trabalhos de treinamento. 

## <a name="prerequisites"></a>Pré-requisitos 
Execute este código em qualquer um destes ambientes:
* Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária
    * Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    * No repositório de [exemplos](https://github.com/Azure/azureml-examples)de Azure Machine Learning, encontre um bloco de anotações concluído navegando até este diretório: **instruções-uso-azureml > ml-frameworks > fastai > Train-com-Custom-Docker** 

* Seu próprio servidor Jupyter Notebook
    * Crie um [arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    * O [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * Um [registro de contêiner do Azure](/azure/container-registry) ou outro registro do Docker que está acessível na Internet.

## <a name="set-up-the-experiment"></a>Configurar o experimento 
Esta seção configura o teste de treinamento inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

### <a name="initialize-a-workspace"></a>Inicializar um workspace
O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparar scripts
Para este tutorial, o script de treinamento **Train.py** é fornecido [aqui](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). Na prática, você pode usar qualquer script de treinamento personalizado, como está, e executá-lo com Azure Machine Learning.

### <a name="define-your-environment"></a>Definir seu ambiente
Crie um objeto de ambiente e habilite o Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A imagem base especificada abaixo dá suporte à biblioteca fast.ai, que permite recursos de aprendizado profundo distribuído. Para obter mais informações, consulte [Fast.ai DockerHub](https://hub.docker.com/u/fastdotai). 

Ao usar sua imagem personalizada do Docker, talvez você já tenha o ambiente do Python configurado corretamente. Nesse caso, defina o `user_managed_dependencies` sinalizador como true para aproveitar o ambiente interno do Python da imagem personalizada. Por padrão, o Azure ML criará um ambiente Conda com dependências especificadas e executará a execução nesse ambiente em vez de usar as bibliotecas do Python que você instalou na imagem base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Para usar uma imagem de um registro de contêiner privado que não está em seu espaço de trabalho, você deve usar `docker.base_image_registry` para especificar o endereço do repositório e um nome de usuário e senha:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Também é possível usar um Dockerfile personalizado. Use essa abordagem se você precisar instalar pacotes não Python como dependências e se lembrar de definir a imagem base como nenhuma.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Para obter mais informações sobre como criar e gerenciar ambientes do Azure ML, consulte [criar & usar ambientes de software](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Criar ou anexar AmlCompute existentes
Você precisará criar um [destino de computação](concept-azure-machine-learning-architecture.md#compute-targets) para treinar seu modelo. Neste tutorial, você criará AmlCompute como seu recurso de computação de treinamento.

A criação de AmlCompute leva aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver em seu espaço de trabalho, esse código ignorará o processo de criação.

Assim como ocorre com outros serviços do Azure, há limites em determinados recursos (por exemplo, AmlCompute) associados ao serviço de Azure Machine Learning. Leia [Este artigo](how-to-manage-quotas.md) sobre os limites padrão e como solicitar mais cota. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Este ScriptRunConfig irá configurar seu trabalho para execução no destino de [computação](how-to-set-up-training-targets.md)desejado.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Envie sua execução
Quando uma execução de treinamento é enviada usando um objeto ScriptRunConfig, o método Submit retorna um objeto do tipo ScriptRun. O objeto ScriptRun retornado fornece acesso programático a informações sobre a execução do treinamento. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning executa scripts de treinamento copiando o diretório de origem inteiro. Se você tiver dados confidenciais que não deseja carregar, use um [arquivo. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, acesse seus dados usando um [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você treinou um modelo usando uma imagem personalizada do Docker. Consulte estes outros artigos para saber mais sobre Azure Machine Learning.
* [Rastrear métricas de execução](how-to-track-experiments.md) durante o treinamento
* [Implante um modelo](how-to-deploy-custom-docker-image.md) usando uma imagem personalizada do Docker.
