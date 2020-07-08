---
title: Treinar e implantar um modelo TensorFlow
titleSuffix: Azure Machine Learning
description: Saiba como executar scripts de treinamento do TensorFlow em escala usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 679e44a8949f283c0e01c47ca3e602ae6fc0eacf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433788"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Crie um modelo de aprendizado profundo TensorFlow em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra como executar seus scripts de treinamento do [TensorFlow](https://www.tensorflow.org/overview) em escala usando a classe [TensorFlow estimadora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) do Azure Machine Learning. Este exemplo treina e registra um modelo TensorFlow para classificar dígitos manuscritos usando uma DNN (rede neural profunda).

Se você estiver desenvolvendo um modelo de TensorFlow do zero ou estiver trazendo um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, poderá usar Azure Machine Learning para expandir os trabalhos de treinamento de software livre para criar, implantar, obter uma versão e monitorar modelos de nível de produção.

Saiba mais sobre o [aprendizado profundo em relação ao aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

 - Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária

     - Conclua o [Tutorial: Configure o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o exemplo de repositório.
    - Na pasta exemplos de aprendizado profundo no servidor do notebook, encontre um bloco de anotações concluído e expandido navegando até este diretório: **como usar-azureml > ml-frameworks > tensorflow > implantação > Train-hiperparameter-ajustar-deploy-com-tensorflow** pasta. 
 
 - Seu próprio servidor Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` de script de exemplo e`utils.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

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

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Criar um experimento de aprendizado profundo

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "TF-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Criar um conjunto de um arquivo

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

Use o `register()` método para registrar o conjunto de dados em seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas, reutilizados em vários experimentos e referenciados por nome em seu script de treinamento.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho do TensorFlow. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

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

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Criar um estimador TensorFlow

O [estimador do TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento do TensorFlow em um destino de computação.

O estimador TensorFlow é implementado por meio da [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe genérica, que pode ser usada para dar suporte a qualquer estrutura. Para ver mais informações sobre modelos de treinamento que usam o estimador genérico, consulte [Treinar modelos com Azure Machine Learning usando o estimador](how-to-train-ml-models.md)

Se o script de treinamento precisar de pacotes PIP ou Conda adicionais para execução, você poderá ter os pacotes instalados na imagem do Docker resultante passando seus nomes pelos `pip_packages` `conda_packages` argumentos e.

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
> O suporte para **Tensorflow 2,0** foi adicionado à classe estimada Tensorflow. Confira a [postagem do blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) para saber mais.

Para obter mais informações sobre como personalizar o ambiente do Python, confira [Criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o estimador do TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registrar ou baixar um modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione os modelos no workspace para simplificar o [gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md). Ao especificar os parâmetros `model_framework` , `model_framework_version` e `resource_configuration` , a implantação do modelo sem código fica disponível. Isso permite que você implante diretamente seu modelo como um serviço Web do modelo registrado, e o `ResourceConfiguration` objeto define o recurso de computação para o serviço Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script de treinamento `mnist-tf.py` , um objeto TensorFlow de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia.

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

O [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador também dá suporte ao treinamento distribuído em clusters de CPU e GPU. Você pode executar facilmente trabalhos TensorFlow distribuídos e Azure Machine Learning gerenciará a orquestração para você.

O Azure Machine Learning é compatível com dois métodos de treinamento distribuído no TensorFlow:

- Treinamento distribuído [baseado em MPI](https://www.open-mpi.org/) usando a estrutura [Horovod](https://github.com/uber/horovod)
- [TensorFlow distribuído](https://www.tensorflow.org/deploy/distributed) nativo usando o método de servidor de parâmetros

### <a name="horovod"></a>Horovod

O [Horovod](https://github.com/uber/horovod) é uma estrutura de código-fonte aberto para treinamento distribuído desenvolvido pelo Uber. Ele oferece um caminho fácil para trabalhos de TensorFlow de GPU distribuídos.

Para usar Horovod, especifique um [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objeto para o `distributed_training` parâmetro no Construtor TensorFlow. Esse parâmetro garante que a biblioteca Horovod esteja instalada para que você use em seu script de treinamento.

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

Para usar o método de servidor de parâmetros, especifique um [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objeto para o `distributed_training` parâmetro no Construtor TensorFlow.

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

#### <a name="define-cluster-specifications-in-tf_config"></a>Definir as especificações de cluster em ' TF_CONFIG '

Você também precisa dos endereços de rede e das portas do cluster para o [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) , portanto Azure Machine Learning define a `TF_CONFIG` variável de ambiente para você.

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

Para a API de alto nível do TensorFlow [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) , o TensorFlow analisa a `TF_CONFIG` variável e cria a especificação do cluster para você.

Para as APIs principais de nível inferior do TensorFlow para treinamento, analise a `TF_CONFIG` variável e compile o `tf.train.ClusterSpec` em seu código de treinamento.

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

O modelo que você acabou de registrar pode ser implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente do estimador usado para treinamento. A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

## <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (versão prévia) para Tensorflow. Ao registrar seu modelo, conforme mostrado acima com os `model_framework` `model_framework_version` parâmetros, e `resource_configuration` , você pode simplesmente usar a `deploy()` função estática para implantar seu modelo.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A implantação [completa abrange](how-to-deploy-and-where.md) em Azure Machine Learning mais detalhadamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo TensorFlow e aprendeu sobre as opções de implantação. Consulte estes outros artigos para saber mais sobre Azure Machine Learning.

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
