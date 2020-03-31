---
title: Treine e implante um modelo TensorFlow
titleSuffix: Azure Machine Learning
description: Aprenda a executar scripts de treinamento TensorFlow em escala usando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228315"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Construa um modelo de aprendizagem profunda tensorFlow em escala com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra como executar seus scripts de treinamento [TensorFlow](https://www.tensorflow.org/overview) em escala usando a classe [de estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) do Azure Machine Learning. Este exemplo treina e registra um modelo TensorFlow para classificar dígitos manuscritos usando uma rede neural profunda (DNN).

Se você está desenvolvendo um modelo TensorFlow a partir do zero ou está trazendo um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, você pode usar o Azure Machine Learning para dimensionar trabalhos de treinamento de código aberto para construir, implantar, versá-lo e monitorar modelos de nível de produção.

Saiba mais sobre [deep learning vs machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes:

 - Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de aprendizado profundo de amostras no servidor do notebook, encontre um notebook completo e expandido navegando para este diretório: **como usar-azureml > > fluxo de tensorflow > implantação > pasta de ajuste de trem-hyperparâmetro-tune-deploy-with-tensorflow.** 
 
 - Seu próprio servidor Jupyter Notebook

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixe os arquivos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` de script de exemplo e`utils.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de amostras do GitHub. O notebook inclui seções expandidas que cobrem ajuste inteligente de hiperparâmetros, implantação de modelos e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção configura o experimento de treinamento carregando os pacotes Python necessários, iniciando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inicialize um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria. No Python SDK, você pode acessar os artefatos [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) do espaço de trabalho criando um objeto.

Crie um objeto de `config.json` espaço de trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Crie um experimento de aprendizagem profunda

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

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

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>Criar um estimador TensorFlow

O [estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento TensorFlow em um alvo de computação.

O estimador TensorFlow é implementado [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para suportar qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [modelos de trem com a azure Machine Learning usando estimador](how-to-train-ml-models.md)

Se o seu script de treinamento precisar de pacotes pip ou conda adicionais para executar, você `pip_packages` `conda_packages` pode ter os pacotes instalados na imagem docker resultante, passando seus nomes através dos argumentos.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> O suporte ao **Tensorflow 2.0** foi adicionado à classe estimador Tensorflow. Confira a [postagem do blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) para saber mais.

Para obter mais informações sobre a personalização do ambiente Python, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Enviar uma corrida

O [objeto Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Execução é executada, ela passa pelas seguintes etapas:

- **Preparação**: Uma imagem do Docker é criada de acordo com o estimador TensorFlow. A imagem é enviada para o registro de contêineres do espaço de trabalho e armazenada em cache para posteriores. Os registros também são transmitidos para o histórico de execução e podem ser visualizados para monitorar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster De IA de lote exigir mais nós para executar a execução do que estão disponíveis atualmente.

- **Execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas do stdout e da pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registre-se ou baixe um modelo

Uma vez treinado o modelo, você pode registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md) Ao especificar `model_framework`os `model_framework_version`parâmetros, e `resource_configuration`a implantação do modelo sem código fica disponível. Isso permite que você implante diretamente seu modelo como um `ResourceConfiguration` serviço web a partir do modelo registrado, e o objeto define o recurso de computação para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script `mnist-tf.py`de treinamento, um objeto de economia TensorFlow persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Executar para baixar uma cópia.

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

O [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador também suporta treinamento distribuído entre os clusters de CPU e GPU. Você pode facilmente executar trabalhos distribuídos do TensorFlow e o Azure Machine Learning gerenciará a orquestração para você.

O Azure Machine Learning é compatível com dois métodos de treinamento distribuído no TensorFlow:

- Treinamento distribuído [baseado em MPI](https://www.open-mpi.org/) usando a estrutura [horovod](https://github.com/uber/horovod)
- [TensorFlow distribuído](https://www.tensorflow.org/deploy/distributed) nativo usando o método do servidor de parâmetros

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) é uma estrutura de código aberto para treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para trabalhos distribuídos de GPU TensorFlow.

Para usar Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) especifique um objeto para o `distributed_training` parâmetro no construtor TensorFlow. Este parâmetro garante que a biblioteca Horovod esteja instalada para você usar em seu script de treinamento.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Servidor de parâmetros

Também é possível executar o [TensorFlow distribuído nativo](https://www.tensorflow.org/deploy/distributed), que usa o modelo de servidor de parâmetros. Nesse método, é preciso treinar em um cluster de servidores de parâmetro e trabalhos. Os trabalhos calculam os gradientes durante o treinamento, enquanto os servidores de parâmetros agregam os gradientes.

Para usar o método do [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) servidor de `distributed_training` parâmetros, especifique um objeto para o parâmetro no construtor TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definir especificações de cluster em 'TF_CONFIG'

Você também precisa dos endereços de [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)rede e portas do cluster `TF_CONFIG` para o , então o Azure Machine Learning define a variável de ambiente para você.

A variável de ambiente `TF_CONFIG` é uma cadeia de caracteres JSON. Confira um exemplo da variável para um servidor de parâmetros:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Para a API de [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) alto nível do TensorFlow, o TensorFlow analisa a `TF_CONFIG` variável e cria a especificação de cluster para você.

Para as APIs de núcleo de nível inferior do `TF_CONFIG` TensorFlow `tf.train.ClusterSpec` para treinamento, analise a variável e construa o em seu código de treinamento.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Implantar um modelo TensorFlow

O modelo que você acabou de registrar pode ser implantado exatamente da mesma forma que qualquer outro modelo registrado no Azure Machine Learning, independentemente de qual estimador você usou para treinamento. A implantação de como fazer contém uma seção sobre modelos de registro, mas você pode pular diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, uma vez que você já tem um modelo registrado.

## <a name="preview-no-code-model-deployment"></a>(Visualização) Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (visualização) para Tensorflow. Ao registrar seu modelo como `model_framework`mostrado `model_framework_version`acima `resource_configuration` com os parâmetros, você pode simplesmente usar a `deploy()` função estática para implantar seu modelo.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

O [how-to](how-to-deploy-and-where.md) completo abrange a implantação no Azure Machine Learning em maior profundidade.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo TensorFlow e aprendeu sobre opções de implantação. Veja esses outros artigos para saber mais sobre o Azure Machine Learning.

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para treinamento de deep learning distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
