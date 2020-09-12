---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implantar seus modelos de Azure Machine Learning, incluindo instâncias de contêiner do Azure, serviço kubernetes do Azure, Azure IoT Edge e matrizes de portão programáveis por campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: fd58adf9e845e296725131da96c3319a72895cb8
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645693"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implantar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como implantar o modelo de aprendizado de máquina como um serviço Web na nuvem do Azure ou para Azure IoT Edge dispositivos.

O fluxo de trabalho é semelhante, independentemente do local de implantação do seu modelo:

1. Registre o modelo.
1. Preparar uma configuração de inferência
1. Preparar um script de entrada (a menos que não use a [implantação sem código](how-to-deploy-no-code-deployment.md))
1. Implante o modelo no destino de computação.
1. Teste o modelo implantado, também chamado de serviço Web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [gerenciar, implantar e monitorar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- A [extensão da CLI (interface de linha de comando) do Azure para o serviço de Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- O [Azure Machine Learning Software Development Kit (SDK) para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

---

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Siga as instruções na documentação do CLI do Azure para [definir seu contexto de assinatura](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Em seguida, faça o:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

para ver os espaços de trabalho aos quais você tem acesso.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte a documentação [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#&preserve-view=trueworkspace) .


---


## <a name="register-your-model"></a><a id="registermodel"></a> Registrar seu modelo

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode, opcionalmente, fornecer metadados sobre o modelo. Os `tags` `properties` dicionários e que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

O `--asset-path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o caminho de uma pasta que contém os arquivos.

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Para incluir vários arquivos no registro do modelo, defina `-p` para o caminho de uma pasta que contém os arquivos.

Para obter mais informações sobre `az ml model register` o, consulte a [documentação de referência](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode, opcionalmente, fornecer metadados sobre o modelo. Os `tags` `properties` dicionários e que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

  Ao usar o SDK para treinar um modelo, você pode receber um objeto de [execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) ou um objeto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registrar um modelo de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação [executar. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

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

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

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

Para obter mais informações, consulte a documentação da [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning, consulte [como implantar um modelo existente](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Se o modelo requer Python
* O [script de entrada](#define-an-entry-script), que é usado para manipular solicitações da Web enviadas ao serviço implantado
* O arquivo Conda que descreve os pacotes do python necessários para a inferência

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

Uma configuração de inferência descreve como configurar o serviço Web que contém seu modelo. Ele é usado posteriormente, quando você implanta o modelo.

A configuração de inferência usa ambientes Azure Machine Learning para definir as dependências de software necessárias para sua implantação. Os ambientes permitem que você crie, gerencie e reutilize as dependências de software necessárias para treinamento e implantação. Você pode criar um ambiente a partir de arquivos de dependência personalizados ou usar um dos ambientes de Azure Machine Learning organizadas. O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência. Observe que você deve indicar os padrões do azureml com a versão >= 1.0.45 como uma dependência Pip, pois ele contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Se você quiser usar a geração de esquema automática, o script de entrada também deverá importar os `inference-schema` pacotes.

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
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

Para obter uma discussão completa sobre como usar e personalizar ambientes Python com Azure Machine Learning, consulte [criar & usar ambientes de software no Azure Machine Learning](how-to-use-environments.md).

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).


O exemplo a seguir demonstra como carregar um ambiente do seu espaço de trabalho e usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte a documentação da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) .

---

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implantação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

As opções disponíveis para uma configuração de implantação diferem de acordo com o destino de computação escolhido.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte a documentação de [implantação do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="python"></a>[Python](#tab/python)

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

---



## <a name="deploy-your-model"></a>Implantar o seu modelo

Agora você está pronto para implantar seu modelo. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Usando um modelo registrado

Se você registrou seu modelo em seu espaço de trabalho Azure Machine Learning, substitua "MyModel: 1" pelo nome do seu modelo e seu número de versão.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Usando um modelo local

Se preferir não registrar seu modelo, você pode passar o parâmetro "sourceDirectory" no seu inferenceconfig.jspara especificar um diretório local a partir do qual servir seu modelo.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra uma implantação local. A sintaxe irá variar dependendo do destino de computação escolhido na etapa anterior.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true), [Model. Deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py&preserve-view=true).

---

### <a name="understanding-service-state"></a>Compreendendo o estado do serviço

Durante a implantação do modelo, você pode ver a alteração do estado do serviço durante a implantação completa.

A tabela a seguir descreve os diferentes Estados de serviço:

| Estado WebService | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | Não |
| Unhealthy | O serviço foi implantado, mas está inacessível no momento.  | Não |
| Não agendável | O serviço não pode ser implantado no momento devido à falta de recursos. | Não |
| Failed (Falha) | O serviço falhou ao ser implantado devido a um erro ou falha. | Sim |
| Íntegros | O serviço está íntegro e o ponto de extremidade está disponível. | Sim |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> Inferência de lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para obter uma explicação sobre a inferência de lote com Azure Machine Learning computação, consulte [como executar previsões de lote](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> Inferência de IoT Edge
O suporte para a implantação no Edge está em versão prévia. Para obter mais informações, consulte [implantar Azure Machine Learning como um módulo IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Excluir recursos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para excluir um WebService implantado, use `az ml service <name of webservice>` .

Para excluir um modelo registrado do seu espaço de trabalho, use `az ml model delete <model id>`

Leia mais sobre como [excluir um WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e [excluir um modelo](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#&preserve-view=truedelete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedelete--).

---


## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)

