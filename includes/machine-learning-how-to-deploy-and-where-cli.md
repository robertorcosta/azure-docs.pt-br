---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542750"
---
## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](../articles/machine-learning/how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- A [extensão da CLI (interface de linha de comando) do Azure para o serviço de Machine Learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

Siga as instruções na documentação do CLI do Azure para [definir seu contexto de assinatura](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Em seguida, faça o:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

para ver os espaços de trabalho aos quais você tem acesso.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrar seu modelo

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode, opcionalmente, fornecer metadados sobre o modelo. Os `tags` `properties` dicionários e que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

O `--asset-path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o caminho de uma pasta que contém os arquivos.

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Para incluir vários arquivos no registro do modelo, defina `-p` para o caminho de uma pasta que contém os arquivos.

Para obter mais informações sobre `az ml model register` o, consulte a [documentação de referência](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Se o modelo requer Python
* O [script de entrada](#define-an-entry-script), que é usado para manipular solicitações da Web enviadas ao serviço implantado
* O arquivo Conda que descreve os pacotes do python necessários para a inferência

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implantação

As opções disponíveis para uma configuração de implantação diferem de acordo com o destino de computação escolhido.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte a documentação de [implantação do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Implantar o seu modelo

Agora você está pronto para implantar seu modelo.

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
## <a name="delete-resources"></a>Excluir recursos

Para excluir um WebService implantado, use `az ml service <name of webservice>` .

Para excluir um modelo registrado do seu espaço de trabalho, use`az ml model delete <model id>`

Leia mais sobre como [excluir um WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e [excluir um modelo](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)