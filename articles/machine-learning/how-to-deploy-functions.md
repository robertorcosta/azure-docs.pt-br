---
title: Implantar modelos ml para aplicativos de funções do Azure (visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure Machine Learning para implantar um modelo em um aplicativo de funções do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927435"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Implantar um modelo de aprendizado de máquina para funções do Azure (visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implantar um modelo do Azure Machine Learning como um aplicativo de função em Funções Azure.

> [!IMPORTANT]
> Embora as funções Azure Machine Learning e Azure estejam geralmente disponíveis, a capacidade de empacotar um modelo do serviço de Aprendizado de Máquina para Funções está em visualização.

Com o Azure Machine Learning, você pode criar imagens Docker a partir de modelos treinados de aprendizado de máquina. O Azure Machine Learning agora tem a funcionalidade de visualização para construir esses modelos de aprendizado de máquina em aplicativos de função, que podem ser [implantados em Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o Criar um artigo [sobre o espaço de trabalho.](how-to-manage-workspace.md)
* A [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](tutorial-train-models-with-aml.md) para treinar e registrar um.

    > [!IMPORTANT]
    > Os trechos de código deste artigo supõem que você definiu as seguintes variáveis:
    >
    > * `ws`- Seu espaço de trabalho de aprendizado de máquina do Azure.
    > * `model`- O modelo registrado que será implantado.
    > * `inference_config`- A configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre a definição dessas variáveis, consulte [Implantar modelos com O Zure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Antes de implantar, você deve definir o que é necessário para executar o modelo como um serviço web. A lista a seguir descreve os itens básicos necessários para uma implantação:

* Um __script de entrada__. Este script aceita solicitações, pontua a solicitação usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico para o seu modelo; ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados devolvidos aos clientes.
    >
    > Se os dados de solicitação forem em um formato que não seja utilizável pelo seu modelo, o script pode transformá-los em um formato aceitável. Ele também pode transformar a resposta antes de retornar ao cliente.
    >
    > Por padrão, ao empacotar para funções, a entrada é tratada como texto. Se você estiver interessado em consumir os bytes brutos da entrada (por exemplo, para gatilhos Blob), você deve usar [AMLRequest para aceitar dados brutos](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Dependências,** como scripts auxiliares ou pacotes Python/Conda necessários para executar o script de entrada ou modelo

Essas entidades são encapsuladas em uma __configuração de inferência.__ A configuração de inferência faz referência ao script de entrada e a outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com funções do Azure, você deve usar um objeto [Ambiente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Observe que se você estiver definindo um ambiente personalizado, você deve adicionar azureml-defaults com a versão >= 1.0.45 como uma dependência de pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. O exemplo a seguir demonstra criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Para obter mais informações sobre ambientes, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração de inferência, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao implantar em Funções, você não precisa criar uma __configuração de implantação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instale o pacote de visualização do SDK para suporte a funções

Para construir pacotes para funções do Azure, você deve instalar o pacote de visualização do SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Docker que é implantada no Azure Functions, use [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) ou a função de pacote específica para o gatilho que você está interessado em usar. O seguinte trecho de código demonstra como criar um novo pacote com um gatilho blob do modelo e configuração de inferência:

> [!NOTE]
> O trecho de código `model` assume que contém um `inference_config` modelo registrado e que contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [Implantar modelos com O Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True`, a saída do processo de compilação Docker é mostrada. Uma vez que o processo é concluído, a imagem foi criada no Registro de Contêineres do Azure para o seu espaço de trabalho. Uma vez que a imagem tenha sido construída, o local no Registro de Contêineres do Azure será exibido. O local retornado está `<acrinstance>.azurecr.io/package@sha256:<hash>`no formato .

> [!NOTE]
> A embalagem para funções atualmente suporta gatilhos HTTP, gatilhos Blob e gatilhos de barramento de serviço. Para obter mais informações sobre os gatilhos, consulte [as vinculações funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Salve as informações de localização, pois elas são usadas ao implantar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo web

1. Use o comando a seguir para obter as credenciais de login do Registro de Contêiner do Azure que contém a imagem. Substitua pelo `<myacr>` valor retornado `package.location`anteriormente de : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída deste comando é semelhante ao seguinte documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salve o valor para __nome de usuário__ e uma das __senhas__.

1. Se você ainda não tiver um grupo de recursos ou um plano de serviço de aplicativo para implantar o serviço, os seguintes comandos demonstram como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, um nível _básico_ `--sku B1`de preços do Linux () é usado.

    > [!IMPORTANT]
    > As imagens criadas pelo Azure Machine Learning `--is-linux` usam o Linux, então você deve usar o parâmetro.

1. Crie a conta de armazenamento para usar para o armazenamento de trabalho na Web e obtenha sua seqüência de conexão. Substitua pelo `<webjobStorage>` nome que deseja usar.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Para criar o aplicativo de função, use o seguinte comando. Substitua pelo `<app-name>` nome que deseja usar. `<acrinstance>` Substitua `<imagename>` e com os `package.location` valores devolvidos anteriormente. Substitua pelo `<webjobStorage>` nome da conta de armazenamento da etapa anterior:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo de função foi criado. No entanto, como você não forneceu a seqüência de conexão para o gatilho de bolha ou credenciais para o Registro de Contêiner do Azure que contém a imagem, o aplicativo de função não está ativo. Nas etapas seguintes, você fornece a seqüência de conexão e as informações de autenticação para o registro do contêiner. 

1. Crie a conta de armazenamento para usar no armazenamento do gatilho blob e obtenha sua seqüência de conexão. Substitua pelo `<triggerStorage>` nome que deseja usar.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Grave esta seqüência de conexão para fornecer ao aplicativo de função. Vamos usá-lo mais tarde quando pedirmos`<triggerConnectionString>`

1. Crie os recipientes para entrada e saída na conta de armazenamento. Substitua-a `<triggerConnectionString>` pela seqüência de conexões repostada anteriormente:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Para associar a seqüência de conexão de gatilho com o aplicativo de função, use o seguinte comando. Substitua pelo `<app-name>` nome do aplicativo de função. Substitua-a `<triggerConnectionString>` pela seqüência de conexões repostada anteriormente:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Você precisará recuperar a tag associada ao contêiner criado usando o seguinte comando. Substitua-o `<username>` pelo nome de usuário retornado anteriormente do registro do contêiner:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Salve o valor devolvido, ele será `imagetag` usado como o na próxima etapa.

1. Para fornecer ao aplicativo de função as credenciais necessárias para acessar o registro de contêineres, use o seguinte comando. Substitua pelo `<app-name>` nome do aplicativo de função. `<acrinstance>` Substitua `<imagetag>` e com os valores da chamada Cli AZ na etapa anterior. `<username>` Substitua `<password>` e com as informações de login do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Este comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Neste ponto, o aplicativo de função começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos até que a imagem seja carregada. Você pode monitorar o progresso usando o Portal Azure.

## <a name="test-the-deployment"></a>Teste a implantação

Uma vez que a imagem tenha sido carregada e o aplicativo esteja disponível, use as seguintes etapas para acionar o aplicativo:

1. Crie um arquivo de texto que contenha os dados que o arquivo score.py espera. O exemplo a seguir funcionaria com um score.py que espera uma matriz de 10 números:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > O formato dos dados depende do que seu score.py e modelo espera.

2. Use o seguinte comando para carregar este arquivo para o recipiente de entrada na bolha de armazenamento de gatilho criada anteriormente. Substitua pelo `<file>` nome do arquivo que contém os dados. Substitua-a `<triggerConnectionString>` pela seqüência de conexões repostada anteriormente. Neste exemplo, `input` está o nome do recipiente de entrada criado anteriormente. Se você usou um nome diferente, substitua este valor:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A saída deste comando é semelhante ao seguinte JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Para visualizar a saída produzida pela função, use o seguinte comando para listar os arquivos de saída gerados. Substitua-a `<triggerConnectionString>` pela seqüência de conexões repostada anteriormente. Neste exemplo, `output` está o nome do recipiente de saída criado anteriormente. Se você usou um nome diferente, substitua este valor::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    A saída deste comando `sample_input_out.json`é semelhante a .

4. Para baixar o arquivo e inspecionar o conteúdo, use o seguinte comando. Substitua pelo `<file>` nome do arquivo retornado pelo comando anterior. Substitua-a `<triggerConnectionString>` pela seqüência de conexões repostada anteriormente: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Assim que o comando estiver concluído, abra o arquivo. Contém os dados devolvidos pelo modelo.

Para obter mais informações sobre o uso de gatilhos blob, consulte a função Criar uma função acionada pelo artigo [de armazenamento Azure Blob.](/azure/azure-functions/functions-create-storage-blob-triggered-function)

## <a name="next-steps"></a>Próximas etapas

* Aprenda a configurar seu aplicativo de funções na documentação [Funções.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Saiba mais sobre os gatilhos de armazenamento Blob [Azure Blob .](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)
* [Implante seu modelo no Azure App Service](how-to-deploy-app-service.md).
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Referência da API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
