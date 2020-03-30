---
title: Treine modelos PyTorch de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar seus scripts de treinamento PyTorch em escala corporativa usando a classe de estimadores PyTorch do Azure Machine Learning.  Os scripts de exemplo classificam as imagens de frango e peru para construir uma rede neural de aprendizagem profunda baseada no tutorial de aprendizado de transferência do PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834853"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Treine modelos de aprendizagem profunda pytorch em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar seus scripts de treinamento [PyTorch](https://pytorch.org/) em escala corporativa usando a classe [de estimator PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) do Azure Machine Learning.  

Os scripts de exemplo deste artigo são usados para classificar imagens de frango e peru para construir uma rede neural de aprendizagem profunda com base no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizado de transferência do PyTorch . 

Se você está treinando um modelo PyTorch de aprendizagem profunda desde o zero ou você está trazendo um modelo existente para a nuvem, você pode usar o Azure Machine Learning para dimensionar trabalhos de treinamento de código aberto usando recursos elásticos de computação em nuvem. Você pode construir, implantar, versa e monitorar modelos de nível de produção com o Azure Machine Learning. 

Saiba mais sobre [deep learning vs machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes:

- Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de aprendizado profundo de amostras no servidor do notebook, encontre um notebook completo e expandido navegando para este diretório: **como usar-azureml > treinamento com aprendizado profundo > pasta de trem-hiperparâmetro-tune-deploy-with-pytorch.** 
 
 - Seu próprio servidor Jupyter Notebook

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixe os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de amostras do GitHub. O notebook inclui seções expandidas que cobrem ajuste inteligente de hiperparâmetros, implantação de modelos e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção configura o experimento de treinamento carregando os pacotes python necessários, iniciando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicialize um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria. No Python SDK, você pode acessar os artefatos [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) do espaço de trabalho criando um objeto.

Crie um objeto de `config.json` espaço de trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Crie um experimento de aprendizagem profunda

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "pitoresco-pássaros".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de dados consiste em cerca de 120 imagens de treinamento cada para perus e galinhas, com 100 imagens de validação para cada classe. Vamos baixar e extrair o conjunto de `pytorch_train.py`dados como parte do nosso script de treinamento . As imagens são um subconjunto do Conjunto de [Dados Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparar scripts de treinamento

Neste tutorial, o roteiro `pytorch_train.py`de treinamento, já está fornecido. Na prática, você pode pegar qualquer script de treinamento personalizado, como é, e executá-lo com o Azure Machine Learning.

Faça upload do script `pytorch_train.py`de treinamento do Pytorch, .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se você quiser usar os recursos de rastreamento e métricas do Azure Machine Learning, você terá que adicionar um código de pequena quantidade dentro do seu script de treinamento. Exemplos de rastreamento de métricas `pytorch_train.py`podem ser encontrados em .

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um alvo de computação para que seu trabalho no PyTorch seja executado. Neste exemplo, crie um cluster de computação azure Machine Learning habilitado para GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre metas de computação, consulte [o que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-pytorch-estimator"></a>Crie um estimador PyTorch

O [estimador PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma maneira simples de lançar um trabalho de treinamento PyTorch em um alvo de computação.

O estimador PyTorch é implementado [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para suportar qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [modelos de trem com a azure Machine Learning usando estimador](how-to-train-ml-models.md)

Se o seu script de treinamento precisar de pacotes pip ou conda adicionais para executar, você `pip_packages` `conda_packages` pode ter os pacotes instalados na imagem do docker resultante, passando seus nomes através dos argumentos.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

Para obter mais informações sobre a personalização do ambiente Python, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Enviar uma corrida

O [objeto Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a Execução é executada, ela passa pelas seguintes etapas:

- **Preparação**: Uma imagem de docker é criada de acordo com o estimador PyTorch. A imagem é enviada para o registro de contêineres do espaço de trabalho e armazenada em cache para posteriores. Os registros também são transmitidos para o histórico de execução e podem ser visualizados para monitorar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster De IA de lote exigir mais nós para executar a execução do que estão disponíveis atualmente.

- **Execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas do stdout e da pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registre-se ou baixe um modelo

Uma vez treinado o modelo, você pode registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> O modelo que você acabou de registrar é implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente de qual estimador você usou para treinamento. A implantação de como fazer contém uma seção sobre modelos de registro, mas você pode pular diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, uma vez que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script `pytorch_train.py`de treinamento, um objeto de salvamento PyTorch persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Executar para baixar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Treinamento distribuído

O [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimador também suporta treinamento distribuído entre os clusters de CPU e GPU. Você pode facilmente executar trabalhos pytorch distribuídos e o Azure Machine Learning gerenciará a orquestração para você.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é um código aberto, todos reduzem a estrutura para treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para trabalhos de GPU PyTorch distribuídos.

Para usar Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) especifique um objeto para o `distributed_training` parâmetro no construtor PyTorch. Este parâmetro garante que a biblioteca Horovod esteja instalada para você usar em seu script de treinamento.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod e suas dependências serão instaladas para você, para que `train.py` você possa importá-lo em seu script de treinamento da seguinte forma:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportar para ONNX

Para otimizar a inferência com o [ONNX Runtime,](concept-onnx.md)converta o modelo PyTorch treinado para o formato ONNX. Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para predição, mais comumente em dados de produção. Veja o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) como exemplo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou uma rede neural de aprendizagem profunda usando PyTorch no Azure Machine Learning. Para aprender como implantar um modelo, continue no nosso artigo de implantação de modelos.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de deep learning distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

