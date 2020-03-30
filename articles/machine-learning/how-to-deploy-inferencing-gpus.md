---
title: Implantar um modelo para inferência com GPU
titleSuffix: Azure Machine Learning
description: Este artigo ensina como usar o Azure Machine Learning para implantar um modelo de aprendizagem profunda tensorflow habilitado para GPU como um serviço web.service e solicitações de inferência de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398343"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implantar um modelo de aprendizagem profunda para inferência com GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo ensina como usar o Azure Machine Learning para implantar um modelo habilitado para GPU como um serviço web. As informações deste artigo são baseadas na implantação de um modelo no Azure Kubernetes Service (AKS). O cluster AKS fornece um recurso de GPU que é usado pelo modelo para inferência.

Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para fazer previsões. O uso de GPUs em vez de CPUs oferece vantagens de desempenho em computação altamente paralelizável.

> [!IMPORTANT]
> Para implantações de serviços web, a inferência de GPU só é suportada no Azure Kubernetes Service. Para inferência usando um __pipeline de aprendizado de máquina,__ as GPUs só são suportadas no Azure Machine Learning Compute. Para obter mais informações sobre o uso de pipelines ML, consulte [Previsões de lote run](how-to-use-parallel-run-step.md). 

> [!TIP]
> Embora os trechos de código deste artigo usem um modelo TensorFlow, você pode aplicar as informações a qualquer estrutura de aprendizado de máquina que suporte GPUs.

> [!NOTE]
> As informações deste artigo baseiam-se nas informações no artigo Como implantar no [Azure Kubernetes Service.](how-to-deploy-azure-kubernetes-service.md) Quando esse artigo geralmente abrange a implantação para a KS, este artigo abrange a implantação específica da GPU.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

* Um ambiente de desenvolvimento Python com o Azure Machine Learning SDK instalado. Para obter mais informações, consulte [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Um modelo registrado que usa uma GPU.

    * Para saber como registrar modelos, consulte [Implantar modelos](how-to-deploy-and-where.md#registermodel).

    * Para criar e registrar o modelo Tensorflow usado para criar este documento, consulte [Como treinar um modelo tensorFlow](how-to-train-tensorflow.md).

* Uma compreensão geral de [como e onde implantar modelos.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

Para se conectar a um espaço de trabalho existente, use o seguinte código:

> [!IMPORTANT]
> Este trecho de código espera que a configuração do espaço de trabalho seja salva no diretório atual ou em seu pai. Para obter mais informações sobre a criação de um espaço de trabalho, consulte [Criar e gerenciar espaços de trabalho do Azure Machine Learning](how-to-manage-workspace.md).   Para obter mais informações sobre como salvar a configuração para arquivar, consulte [Criar um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Crie um cluster Kubernetes com GPUs

O Azure Kubernetes Service oferece muitas opções diferentes de GPU. Você pode usar qualquer um deles para inferência de modelo. Consulte [a lista de VMs da série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para obter uma análise completa dos recursos e custos.

O código a seguir demonstra como criar um novo cluster AKS para o seu espaço de trabalho:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> O Azure cobrará enquanto o cluster AKS existir. Certifique-se de excluir seu cluster AKS quando terminar com ele.

Para obter mais informações sobre como usar o AKS com o Azure Machine Learning, consulte [Como implantar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Escreva o script de entrada

O script de entrada recebe dados enviados ao serviço web, passa-os para o modelo e retorna os resultados de pontuação. O script a seguir carrega o modelo Tensorflow na inicialização e, em seguida, usa o modelo para marcar dados.

> [!TIP]
> O script de entrada é específico para cada modelo. Por exemplo, o script deve saber a estrutura a ser usada com seu modelo, formatos de dados, etc.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Este arquivo `score.py`é chamado . Para obter mais informações sobre scripts de entrada, consulte [Como e onde implantar](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Defina o ambiente conda

O arquivo de ambiente conda especifica as dependências do serviço. Inclui dependências exigidas pelo modelo e pelo script de entrada. Observe que você deve indicar azureml-defaults com verion >= 1.0.45 como uma dependência pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. O YAML a seguir define o ambiente para um modelo Tensorflow. Ele especifica `tensorflow-gpu`, que fará uso da GPU usada nesta implantação:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Para este exemplo, o `myenv.yml`arquivo é salvo como .

## <a name="define-the-deployment-configuration"></a>Defina a configuração de implantação

A configuração de implantação define o ambiente Azure Kubernetes Service usado para executar o serviço web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Para obter mais informações, consulte a documentação de referência [de AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Defina a configuração da inferência

A configuração de inferência aponta para o script de entrada e um objeto de ambiente, que usa uma imagem docker com suporte a GPU. Observe que o arquivo YAML usado para definição de ambiente deve listar azureml-defaults com a versão >= 1.0.45 como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md)
Para obter mais informações, consulte a documentação de referência para [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Implantar o modelo

Implante o modelo no cluster AKS e espere que ele crie seu serviço.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Se `InferenceConfig` o `enable_gpu=True`objeto tiver, então o parâmetro deve fazer referência a `deployment_target` um cluster que forneça uma GPU. Caso contrário, a implantação falhará.

Para obter mais informações, consulte a documentação de referência para [Modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Emita uma consulta de amostra para o seu serviço

Envie uma consulta de teste para o modelo implantado. Quando você envia uma imagem jpeg para o modelo, ela marca a imagem. A amostra de código a seguir baixa dados de teste e, em seguida, seleciona uma imagem de teste aleatória para enviar ao serviço.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Para obter mais informações sobre a criação de um aplicativo cliente, consulte [Criar cliente para consumir o serviço web implantado](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se você criou o cluster AKS especificamente para este exemplo, exclua seus recursos depois que você terminar.

> [!IMPORTANT]
> O Azure fatura com base em quanto tempo o cluster AKS é implantado. Certifique-se de limpá-lo depois que você terminar com ele.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelo no FPGA](how-to-deploy-fpga-web-service.md)
* [Implantar modelo com ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Modelos DNN do Trem Tensorflow](how-to-train-tensorflow.md)
