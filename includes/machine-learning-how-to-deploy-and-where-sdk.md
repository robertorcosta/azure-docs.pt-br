---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 97f0412141f15ad0a72c02b92cfcf089b61db0cf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120330"
---
## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](../articles/machine-learning/how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- O [Azure Machine Learning Software Development Kit (SDK) para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte a documentação [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

## <a name="register-your-model"></a><a id="registermodel"></a>Registrar seu modelo

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode, opcionalmente, fornecer metadados sobre o modelo. Os `tags` `properties` dicionários e que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

  Ao usar o SDK para treinar um modelo, você pode receber um objeto de [execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registrar um modelo de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação [executar. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrar um modelo de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` não são especificados, portanto, a iteração com a melhor métrica primária será registrada. O `model_id` valor retornado da execução é usado em vez de um nome de modelo.

    Para obter mais informações, consulte a documentação do [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar esse método para registrar modelos treinados com Azure Machine Learning e, em seguida, baixados. Você também pode usar esse método para registrar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

+ **Usando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos.

Para obter mais informações, consulte a documentação da [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning, consulte [como implantar um modelo existente](../articles/machine-learning/how-to-deploy-existing-model.md).

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inferenceconfig"></a>Definir um InferenceConfig

Uma configuração de inferência descreve como configurar o serviço Web que contém seu modelo. Ele é usado posteriormente, quando você implanta o modelo.

A configuração de inferência usa ambientes Azure Machine Learning para definir as dependências de software necessárias para sua implantação. Os ambientes permitem que você crie, gerencie e reutilize as dependências de software necessárias para treinamento e implantação. Você pode criar um ambiente a partir de arquivos de dependência personalizados ou usar um dos ambientes de Azure Machine Learning organizadas. O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência. Observe que você deve indicar os padrões do azureml com a versão >= 1.0.45 como uma dependência Pip, pois ele contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Se você quiser usar a geração de esquema automática, o script de entrada também deverá importar os `inference-schema` pacotes.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se sua dependência estiver disponível por meio de Conda e PIP (de PyPi), a Microsoft recomenda usar a versão Conda, já que os pacotes Conda normalmente são fornecidos com binários pré-criados que tornam a instalação mais confiável.
>
> Para obter mais informações, consulte [Understanding Conda and Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Para verificar se sua dependência está disponível por meio do Conda, use o `conda search <package-name>` comando ou use os índices de pacote em [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Você pode usar o arquivo de dependências para criar um objeto de ambiente e salvá-lo em seu espaço de trabalho para uso futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Para obter uma discussão completa sobre como usar e personalizar ambientes Python com Azure Machine Learning, consulte [criar & usar ambientes de software no Azure Machine Learning](../articles/machine-learning/how-to-use-environments.md)

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


O exemplo a seguir demonstra como carregar um ambiente do seu espaço de trabalho e usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](../articles/machine-learning/how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte a documentação da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deploymentconfiguration"></a>Definir um DeploymentConfiguration

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implantação é específica para o destino de computação que hospedará o serviço Web.* Por exemplo, ao implantar um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do script de entrada. Ele é usado para definir as características do destino de computação que hospedará o modelo e o script de entrada.

Talvez você também precise criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância do AKS (serviço kubernetes do Azure) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instâncias de Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para local, instâncias de contêiner do Azure e serviços Web AKS podem ser importadas de `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

## <a name="deploy-your-model"></a>Implantar o seu modelo

Agora você está pronto para implantar seu modelo. O exemplo a seguir demonstra uma implantação local. A sintaxe irá variar dependendo do destino de computação escolhido na etapa anterior.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

## <a name="delete-resources"></a>Excluir recursos

Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).