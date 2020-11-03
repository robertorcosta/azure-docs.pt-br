---
title: 'Tutorial: Introdução ao machine learning – Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você começará a usar o SDK do Azure Machine Learning para Python em execução no seu ambiente de desenvolvimento pessoal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2e01721b4b414455b47a394087192696e1ecb025
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892727"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Tutorial: Introdução ao Azure Machine Learning no seu ambiente de desenvolvimento (parte 1 de 4)

Nesta *série de tutoriais de quatro partes* , você aprenderá os conceitos básicos do Azure Machine Learning e concluirá as tarefas de machine learning do Python baseadas em trabalhos na plataforma de nuvem do Azure. 

Na Parte 1 desta série de tutoriais, você vai:

> [!div class="checklist"]
> * Instalar o SDK do Azure Machine Learning.
> * Configurar a estrutura de diretório para o código.
> * Criar um Workspace de Azure Machine Learning.
> * Configurar seu ambiente de desenvolvimento local.
> * Configurar um cluster de cálculo.

> [!NOTE]
> Esta série de tutoriais tem como foco os conceitos do Azure Machine Learning adequados para tarefas de machine learning *baseadas em trabalho* do Python com uso intenso de computação e/ou que exigem capacidade de reprodução. Se você estiver mais interessado em um fluxo de trabalho exploratório, poderá usar o [Jupyter ou RStudio em uma instância de computação do Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente o [Azure Machine Learning](https://aka.ms/AMLFree).
- Familiaridade com o Python e com os [conceitos de Machine Learning](concept-azure-machine-learning-architecture.md). Os exemplos incluem ambientes, treinamento e pontuação.
- Ambiente de desenvolvimento local, como o Visual Studio Code, o Jupyter ou o PyCharm.
- Python (versão 3.5 a 3.7).


## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o SDK do Azure Machine Learning

Neste tutorial, usaremos o SDK do Python do Azure Machine Learning para Python.

Você pode usar as ferramentas mais conhecidas para você (por exemplo, o Conda e o pip) para configurar um ambiente do Python para uso neste tutorial. Instale o SDK do Azure Machine Learning para Python por meio do pip no ambiente do Python:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Criar uma estrutura de diretório para o código
Recomendamos que você configure a seguinte estrutura de diretório simples para este tutorial:

```markdown
tutorial
└──.azureml
```

- `tutorial`: diretório de nível superior do projeto.
- `.azureml`: subdiretório oculto usado para armazenar arquivos de configuração do Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Criar um workspace do Azure Machine Learning

Um workspace é um recurso de nível superior para Azure Machine Learning e é um local centralizado para:

- Gerenciar recursos como computação.
- Armazenar ativos como notebooks, ambientes, conjuntos de dados, pipelines, modelos e pontos de extremidade.
- Colaborar com outros membros da equipe.

No diretório de nível superior, `tutorial`, adicione um novo arquivo do Python chamado `01-create-workspace.py` usando o código a seguir. Adapte os parâmetros (nome, ID da assinatura, grupo de recursos e [localização](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) conforme suas preferências.

Execute o código em uma sessão interativa ou como um arquivo do Python.

>[!NOTE]
> Quando estiver usando um ambiente de desenvolvimento local (por exemplo, seu computador), você precisará se autenticar no seu workspace usando um *código de dispositivo* na primeira vez que executar o código a seguir. Siga as instruções na tela.

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

> [!TIP]
> Se a execução desse código fornecer um erro informando que você não tem acesso à assinatura, confira [Criar um workspace](how-to-manage-workspace.md?tab=python#create-multi-tenant) para obter informações sobre as opções de autenticação.


Depois de executar com êxito *01-create-workspace.py* , sua estrutura de pastas terá a seguinte aparência:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

O arquivo `.azureml/config.json` contém os metadados necessários para se conectar ao seu Workspace do Azure Machine Learning. Ou seja, ele contém a ID da assinatura, o grupo de recursos e o nome do workspace. 

> [!NOTE]
> O conteúdo de `config.json` não são segredos. Não há problemas em compartilhar esses detalhes.
>
> A autenticação ainda é necessária para interagir com seu workspace do Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de computação do Azure Machine Learning

Crie um script Python no diretório de nível superior `tutorial` chamado `02-create-compute.py`. Preencha-o com o seguinte código para criar um cluster de cálculo do Azure Machine Learning que fará o dimensionamento automático entre zero e quatro nós:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Execute o arquivo do Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster for criado, ele não terá nenhum nó provisionado. O cluster *não* gera custos até que você envie um trabalho. Esse cluster será reduzido quando ficar ocioso por 2.400 segundos (40 minutos).

Sua estrutura de pastas agora terá a seguinte aparência:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de configuração, você:

- Criou um Workspace do Azure Machine Learning.
- Configurou seu ambiente de desenvolvimento local.
- Criou um cluster de computação do Azure Machine Learning.

Nas outras partes deste tutorial você aprenderá a:

* Parte 2. Executar o código na nuvem usando o SDK do Azure Machine Learning para Python.
* Parte 3. Gerenciar o ambiente do Python que você usa para treinamento de modelo.
* Parte 4. Carregar dados no Azure e consumi-los no treinamento.

Passe para o próximo tutorial para aprender a enviar um script para o cluster de Computação do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Executar um aplicativo "Olá, Mundo!" Script Python no Azure](tutorial-1st-experiment-hello-world.md)
