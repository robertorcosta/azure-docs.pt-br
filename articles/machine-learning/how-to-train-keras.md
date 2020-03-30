---
title: Treinar modelos keras de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a treinar e registrar um modelo de classificação de rede neural profunda keras em execução no TensorFlow usando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269959"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Treine e registre um modelo de classificação Keras com Aprendizado de Máquina Do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra como treinar e registrar um modelo de classificação Keras construído no TensorFlow usando o Azure Machine Learning. Ele usa o popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos usando uma rede neural profunda (DNN) construída usando a [biblioteca Keras Python](https://keras.io) em execução em cima do [TensorFlow](https://www.tensorflow.org/overview).

Keras é uma API de rede neural de alto nível capaz de executar o topo de outras estruturas dnn populares para simplificar o desenvolvimento. Com o Azure Machine Learning, você pode escalar rapidamente trabalhos de treinamento usando recursos elásticos de computação em nuvem. Você também pode acompanhar suas corridas de treinamento, modelos de versão, implantar modelos e muito mais.

Se você está desenvolvendo um modelo Keras desde o zero ou trazendo um modelo existente para a nuvem, o Azure Machine Learning pode ajudá-lo a construir modelos prontos para a produção.

Veja o [artigo conceitual](concept-deep-learning-vs-machine-learning.md) para obter informações sobre as diferenças entre machine learning e deep learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes:

- Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de amostras no servidor do notebook, encontre um notebook completo e expandido navegando para este diretório: **como usar-azureml > treinamento com aprendizado profundo > pasta de trem-hiperparâmetro-tune-deploy-with-keras.**

 - Seu próprio servidor Jupyter Notebook

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixe os arquivos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` de script de exemplo e`utils.py`

    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia na página de amostras do GitHub. O notebook inclui seções expandidas que cobrem ajuste inteligente de hiperparâmetros, implantação de modelos e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção configura o experimento de treinamento carregando os pacotes python necessários, iniciando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicialize um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria. No Python SDK, você pode acessar os artefatos [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) do espaço de trabalho criando um objeto.

Crie um objeto de `config.json` espaço de trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento chamado "keras-mnist" em seu espaço de trabalho.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Criar um conjunto de dados de arquivo

Um objeto `FileDataset` referencia um ou vários arquivos no armazenamento de dados do workspace ou em URLs públicas. Os arquivos podem ser de qualquer formato e a classe fornece a capacidade de baixar ou montar os arquivos no computador. Ao criar um `FileDataset`, você cria uma referência à localização da fonte de dados. Se você tiver aplicado todas as transformações ao conjunto de dados, elas também serão armazenadas no conjunto de dados. Os dados permanecem na localização existente, portanto, nenhum custo de armazenamento extra é gerado. Confira o guia de [instruções](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) no pacote `Dataset` para obter mais informações.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Use `register()` o método para registrar o conjunto de dados no seu espaço de trabalho para que eles possam ser compartilhados com outros, reutilizados em vários experimentos e referidos pelo nome em seu script de treinamento.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um alvo de computação para que seu trabalho tensorFlow seja executado. Neste exemplo, crie um cluster de computação azure Machine Learning habilitado para GPU.

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Crie um estimador TensorFlow e importe Keras

O [estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de lançar trabalhos de treinamento tensorFlow no alvo da computação. Uma vez que o Keras é executado em cima do TensorFlow, você pode `pip_packages` usar o estimador TensorFlow e importar a biblioteca Keras usando o argumento.

Primeiro obtenha os dados do datastore `Dataset` do espaço de trabalho usando a classe.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

O estimador TensorFlow é implementado [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para suportar qualquer estrutura. Além disso, crie `script_params` um dicionário que contenha as configurações do hiperparâmetro DNN. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [modelos de trem com a azure Machine Learning usando estimador](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Enviar uma corrida

O [objeto Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Execução é executada, ela passa pelas seguintes etapas:

- **Preparação**: Uma imagem de docker é criada de acordo com o estimador TensorFlow. A imagem é enviada para o registro de contêineres do espaço de trabalho e armazenada em cache para posteriores. Os registros também são transmitidos para o histórico de execução e podem ser visualizados para monitorar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster De IA de lote exigir mais nós para executar a execução do que estão disponíveis atualmente.

- **Execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas do stdout e da pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="register-the-model"></a>Registre o modelo

Depois de treinar o modelo DNN, você pode registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> O modelo que você acabou de registrar é implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente de qual estimador você usou para treinamento. A implantação de como fazer contém uma seção sobre modelos de registro, mas você pode pular diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, uma vez que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer trabalhos adicionais de validação de modelos localmente. No script de `mnist-keras.py`treinamento, um objeto de economia TensorFlow persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Executar para baixar uma cópia do datastore.

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

Neste artigo, você treinou e registrou um modelo Keras no Azure Machine Learning. Para aprender como implantar um modelo, continue no nosso artigo de implantação de modelos.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de deep learning distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
