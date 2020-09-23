---
title: 'Tutorial:  Introdução a machine learning – Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você começará a usar o SDK do Python do Azure Machine Learning em execução no seu ambiente de desenvolvimento pessoal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929180"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Tutorial: Introdução ao Azure Machine Learning em seu ambiente de desenvolvimento (Parte 1 de 4)

Nesta **série de tutoriais de quatro partes**, você aprenderá os conceitos básicos do Azure Machine Learning e as tarefas de ML do Python baseadas em trabalhos completos na nuvem do Azure, incluindo:

1. Configurar um workspace e seu ambiente de desenvolvedor do Machine Learning local.
2. Executar o código na nuvem usando o SDK do Python do Azure Machine Learning.
3. Gerenciar o ambiente do Python que você usa para treinamento de modelo.
4. Carregar dados no Azure e consumi-los no treinamento.

Na **Parte 1 desta série de tutoriais**, você vai:

> [!div class="checklist"]
> * Instalar o SDK do Azure Machine Learning
> * Configurar a estrutura do diretório para o código
> * Criar um workspace do Azure Machine Learning
> * Configurar seu ambiente de desenvolvimento
> * Configurar um cluster de computação

>[!NOTE]
> Esta série de tutoriais concentra-se nos conceitos de Azure Machine Learning adequados para tarefas de machine learning __baseadas em trabalho__ do Python com uso intenso de computação e/ou que exigem capacidade de reprodução. Se suas tarefas de machine learning não se enquadram nesse perfil, use a [funcionalidade do Jupyter ou RStudio em uma instância de computação do Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md) para integração ao Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente o [Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.
- Familiaridade com o Python e com os [conceitos de Machine Learning](concept-azure-machine-learning-architecture.md). Por exemplo, ambientes, treinamento, pontuação e assim por diante.
- Um ambiente de desenvolvimento local –um laptop com Python instalado e seu IDE favorito (por exemplo: VSCode, Pycharm, Jupyter etc.).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o SDK do Azure Machine Learning

Ao longo deste tutorial, usamos o SDK do Python do Azure Machine Learning.

Você pode usar as ferramentas mais conhecidas para você (por exemplo: conda, pip etc.) para configurar um ambiente para usar em todo este tutorial. Instale no ambiente o SDK do Python do Azure Machine Learning usando o pip:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Criar estrutura de diretório para código
Recomendamos que você tenha o seguinte diretório simples configurado para este tutorial:

```markdown
tutorial
└──.azureml
```

- **tutorial** (diretório de nível superior do projeto)
- **.azureml** (subdiretório oculto do tutorial):  O diretório `.azureml` é usado para armazenar arquivos de configuração do Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Criar um Workspace do Azure Machine Learning

Um workspace é um recurso de nível superior para Azure Machine Learning e é um local centralizado para:

- Gerenciar recursos como computação
- Armazenar ativos como notebooks, ambientes, conjuntos de valores, pipelines, modelos, pontos de extremidade e assim por diante
- Colaborar com outros membros da equipe

No diretório superior pai – `tutorial` – adicione um arquivo Python chamado `01-create-workspace.py` com o código a seguir. Adapte os parâmetros (nome, ID da assinatura, grupo de recursos e [local](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) conforme suas preferências.

Você pode executar o código em uma sessão interativa ou como um arquivo Python.

>[!NOTE]
> Ao usar um ambiente de desenvolvimento local (por exemplo, laptop), você será solicitado a se autenticar no seu workspace usando um *código de dispositivo* na primeira vez em que executar o código a seguir. Siga as instruções na tela.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Execute este código no diretório `tutorial`:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Depois que o snippet de código acima tiver sido executado, a estrutura de pastas terá a seguinte aparência:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

O arquivo `.azureml/config.json` contém os metadados necessários para se conectar ao seu workspace do Azure Machine Learning, ou seja, sua ID de assinatura, grupo de recursos e nome do workspace. 

> [!NOTE]
> O conteúdo de `config.json` não são segredos – é perfeitamente adequado compartilhar esses dados.
> A autenticação ainda é necessária para interagir com seu workspace do Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de computação do Azure Machine Learning

Crie um script do Python no diretório de nível superior `tutorial` chamado `02-create-compute.py` e preencha com o código a seguir para criar um cluster de computação do Azure Machine Learning cuja escala será ajustada automaticamente entre zero e quatro nós:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Execute o arquivo do Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster tiver sido criado, ele terá 0 nó provisionado. Portanto, o cluster **não gera custos** até que você envie um trabalho. Esse cluster será reduzido quando ficar ocioso por 2.400 segundos (40 minutos).

Sua estrutura de pastas agora terá a seguinte aparência:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de instalação, você:

- Criou um Workspace do Azure Machine Learning
- Configurar seu ambiente de desenvolvimento
- Criou um cluster de computação do Azure Machine Learning.

No próximo tutorial, você verá como enviar um script para o cluster de computação do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Executar o script do Python "Olá, Mundo" no Azure](tutorial-1st-experiment-hello-world.md)
