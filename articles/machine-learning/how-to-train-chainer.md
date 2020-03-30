---
title: Treinar modelos chainer de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar seus scripts de treinamento PyTorch em escala corporativa usando a classe de estimador do Azure Machine Learning Chainer.  O script de exemplo classifica imagens de dígitos manuscritos para construir uma rede neural de aprendizagem profunda usando a biblioteca Chainer Python em execução em cima de numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536610"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Treinar e registrar modelos Chainer em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar seus scripts de treinamento [chainer](https://chainer.org/) em escala corporativa usando a classe estimador Azure Machine Learning [Chainer.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) O exemplo do script de treinamento neste artigo usa o popular conjunto de [dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos usando uma rede neural profunda (DNN) construída usando a biblioteca Chainer Python em execução em cima do [numpy](https://www.numpy.org/).

Se você está treinando um modelo de Chainer de aprendizagem profunda desde o zero ou você está trazendo um modelo existente para a nuvem, você pode usar o Azure Machine Learning para dimensionar trabalhos de treinamento de código aberto usando recursos elásticos de computação em nuvem. Você pode construir, implantar, versa e monitorar modelos de nível de produção com o Azure Machine Learning. 

Saiba mais sobre [deep learning vs machine learning](concept-deep-learning-vs-machine-learning.md).

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes:

- Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de aprendizado profundo de amostras no servidor do notebook, encontre um notebook completo e arquivos no > de **uso-azureml > chainer > implantação > pasta train-hyperparameter-tune-deploy-with-chainer.**  O notebook inclui seções expandidas que cobrem ajuste inteligente de hiperparâmetros, implantação de modelos e widgets de notebook.

- Seu próprio servidor Jupyter Notebook

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - Baixe o arquivo de script de exemplo [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) deste guia na página amostras do GitHub. O notebook inclui seções expandidas que cobrem ajuste inteligente de hiperparâmetros, implantação de modelos e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção configura o experimento de treinamento carregando os pacotes python necessários, iniciando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe a biblioteca Python azureml.core e exiba o número da versão.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicialize um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria. No Python SDK, você pode acessar os artefatos [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) do espaço de trabalho criando um objeto.

Crie um objeto de `config.json` espaço de trabalho lendo o arquivo criado na [seção pré-requisitos:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Criar um diretório de projetos
Crie um diretório que contenha todo o código necessário da sua máquina local a que você precisará acessar no recurso remoto. Isso inclui o script de treinamento e quaisquer arquivos adicionais do qual seu script de treinamento depende.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparar o script de treinamento

Neste tutorial, o script de treinamento **chainer_mnist.py** já está previsto para você. Na prática, você deve ser capaz de pegar qualquer script de treinamento personalizado como está e executá-lo com o Azure ML sem ter que modificar seu código.

Para usar os recursos de rastreamento e métricas do Azure ML, adicione uma pequena quantidade de código Azure ML dentro do seu script de treinamento.  O script de treinamento **chainer_mnist.py** mostra como registrar algumas métricas `Run` para o seu Azure ML executado usando o objeto dentro do script.

O script de treinamento fornecido usa `datasets.mnist.get_mnist` dados de exemplo da função chainer.  Para seus próprios dados, você pode precisar usar etapas como [upload de conjunto de dados e scripts](how-to-train-keras.md#data-upload) para disponibilizar dados durante o treinamento.

Copie o script de treinamento **chainer_mnist.py** em seu diretório de projeto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Crie um experimento de aprendizagem profunda

Criar um experimento. Neste exemplo, crie um experimento chamado "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Criar ou obter um alvo de computação

Você precisa de um [alvo de computação](concept-compute-target.md) para treinar seu modelo. Neste exemplo, você usa a computação gerenciada do Azure ML (AmlCompute) para seu recurso de computação de treinamento remoto.

**A criação do AmlCompute leva aproximadamente 5 minutos**. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, este código pulará o processo de criação.  

```Python
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

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Para obter mais informações sobre metas de computação, consulte [o que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-chainer-estimator"></a>Criar um estimador Chainer

O [estimador Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornece uma maneira simples de lançar trabalhos de treinamento chainer em seu alvo de computação.

O estimador Chainer é implementado [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para apoiar qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [modelos de trem com a azure Machine Learning usando estimador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Enviar uma corrida

O [objeto Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Execução é executada, ela passa pelas seguintes etapas:

- **Preparação**: Uma imagem de docker é criada de acordo com o estimador Chainer. A imagem é enviada para o registro de contêineres do espaço de trabalho e armazenada em cache para posteriores. Os registros também são transmitidos para o histórico de execução e podem ser visualizados para monitorar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster De IA de lote exigir mais nós para executar a execução do que estão disponíveis atualmente.

- **Execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas do stdout e da pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Uma vez treinado o modelo, você pode salvá-lo e registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md)


Após a conclusão do treinamento do modelo, registre o modelo no seu espaço de trabalho com o seguinte código.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> O modelo que você acabou de registrar é implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente de qual estimador você usou para treinamento. A implantação de como fazer contém uma seção sobre modelos de registro, mas você pode pular diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, uma vez que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer trabalhos adicionais de validação de modelos localmente. No script de `chainer_mnist.py`treinamento, um objeto poupador persiste o modelo para uma pasta local (local para o alvo de computação). Você pode usar o objeto Executar para baixar uma cópia do datastore.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou uma rede neural de aprendizagem profunda usando chainer no Azure Machine Learning. Para aprender como implantar um modelo, continue no nosso artigo [de implantação de modelos.](how-to-deploy-and-where.md)

* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)

* [Veja nossa arquitetura de referência para treinamento de deep learning distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
