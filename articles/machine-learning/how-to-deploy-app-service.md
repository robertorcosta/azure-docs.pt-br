---
title: Implantar modelos ml no Azure App Service (visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure Machine Learning para implantar um modelo em um aplicativo web no Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282810"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implantar um modelo de aprendizado de máquina no Azure App Service (visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implantar um modelo do Azure Machine Learning como um aplicativo web no Azure App Service.

> [!IMPORTANT]
> Embora tanto o Azure Machine Learning quanto o Azure App Service estejam geralmente disponíveis, a capacidade de implantar um modelo do serviço de Machine Learning para o App Service está em pré-visualização.

Com o Azure Machine Learning, você pode criar imagens Docker a partir de modelos treinados de aprendizado de máquina. Esta imagem contém um serviço web que recebe dados, envia-os para o modelo e, em seguida, retorna a resposta. O Azure App Service pode ser usado para implantar a imagem e fornece os seguintes recursos:

* [Autenticação](/azure/app-service/configure-authentication-provider-aad) avançada para maior segurança. Os métodos de autenticação incluem tanto o Azure Active Directory quanto o auth multifatorial.
* [Escala automática](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) sem ter que reimplantar.
* [Suporte tls](/azure/app-service/configure-ssl-certificate-in-code) para comunicações seguras entre clientes e o serviço.

Para obter mais informações sobre os recursos fornecidos pelo Azure App Service, consulte a visão geral do [App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Se você precisar da capacidade de registrar os dados de pontuação usados com seu modelo implantado ou os resultados da pontuação, você deve, em vez disso, implantar no Azure Kubernetes Service. Para obter mais informações, consulte [Coletar dados sobre seus modelos de produção](how-to-enable-data-collection.md).

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

    > [!IMPORTANT]
    > O Azure Machine Learning SDK não fornece uma maneira para o serviço web acessar seu armazenamento de dados ou conjuntos de dados. Se você precisar do modelo implantado para acessar dados armazenados fora da implantação, como em uma conta do Azure Storage, você deve desenvolver uma solução de código personalizada usando o SDK relevante. Por exemplo, o [SDK de armazenamento Azure para Python](https://github.com/Azure/azure-storage-python).
    >
    > Outra alternativa que pode funcionar para o seu cenário são [as previsões em lote,](how-to-use-parallel-run-step.md)que fornecem acesso aos datastores ao marcar.

    Para obter mais informações sobre scripts de entrada, consulte [Implantar modelos com O Zure Machine Learning](how-to-deploy-and-where.md).

* **Dependências,** como scripts auxiliares ou pacotes Python/Conda necessários para executar o script de entrada ou modelo

Essas entidades são encapsuladas em uma __configuração de inferência.__ A configuração de inferência faz referência ao script de entrada e a outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com o Azure App Service, você deve usar um objeto [Ambiente.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Observe que se você estiver definindo um ambiente personalizado, você deve adicionar azureml-defaults com a versão >= 1.0.45 como uma dependência de pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. O exemplo a seguir demonstra criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> Ao implantar no Azure App Service, você não precisa criar uma __configuração de implantação__.

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Docker implantada no Azure App Service, use [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). O seguinte trecho de código demonstra como construir uma nova imagem a partir da configuração do modelo e da inferência:

> [!NOTE]
> O trecho de código `model` assume que contém um `inference_config` modelo registrado e que contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [Implantar modelos com O Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando `show_output=True`, a saída do processo de compilação Docker é mostrada. Uma vez que o processo é concluído, a imagem foi criada no Registro de Contêineres do Azure para o seu espaço de trabalho. Uma vez que a imagem tenha sido construída, o local no Registro de Contêineres do Azure será exibido. O local retornado está `<acrinstance>.azurecr.io/package:<imagename>`no formato . Por exemplo, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Salve as informações de localização, pois elas são usadas ao implantar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo web

1. Use o comando a seguir para obter as credenciais de login do Registro de Contêiner do Azure que contém a imagem. Substitua por `<acrinstance>` valor th e `package.location`retornado anteriormente de :

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

    Neste exemplo, um __nível__ básico`--sku B1`de preços () é usado.

    > [!IMPORTANT]
    > As imagens criadas pelo Azure Machine Learning `--is-linux` usam o Linux, então você deve usar o parâmetro.

1. Para criar o aplicativo web, use o seguinte comando. Substitua pelo `<app-name>` nome que deseja usar. Substituir `<acrinstance>` `<imagename>` e com os `package.location` valores retornados anteriormente:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Este comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo web foi criado. No entanto, como você não forneceu as credenciais para o Registro de Contêineres do Azure que contém a imagem, o aplicativo web não está ativo. Na etapa seguinte, você fornece as informações de autenticação para o registro do contêiner.

1. Para fornecer ao aplicativo web as credenciais necessárias para acessar o registro de contêineres, use o seguinte comando. Substitua pelo `<app-name>` nome que deseja usar. `<acrinstance>` Substitua `<imagename>` e com os `package.location` valores devolvidos anteriormente. `<username>` Substitua `<password>` e com as informações de login do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Neste ponto, o aplicativo web começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos até que a imagem seja carregada. Para monitorar o progresso, use o seguinte comando:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Uma vez que a imagem tenha sido carregada e o `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`site esteja ativo, o registro exibe uma mensagem que afirma .

Uma vez que a imagem é implantada, você pode encontrar o nome de host usando o seguinte comando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Este comando retorna informações semelhantes `<app-name>.azurewebsites.net`ao seguinte nome de host - . Use esse valor como parte da __url base__ para o serviço.

## <a name="use-the-web-app"></a>Use o Web App

O serviço web que passa solicitações `{baseurl}/score`para o modelo está localizado em . Por exemplo, `https://<app-name>.azurewebsites.net/score`. O código Python a seguir demonstra como enviar dados para a URL e exibir a resposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Próximas etapas

* Aprenda a configurar seu Aplicativo web na documentação [do App Service on Linux.](/azure/app-service/containers/)
* Saiba mais sobre o dimensionamento em [Comece com a Autoscale no Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Use um certificado TLS/SSL no seu Serviço de Aplicativos Azure](/azure/app-service/configure-ssl-certificate-in-code).
* [Configure o aplicativo App Service para usar o login do Azure Active Directory](/azure/app-service/configure-authentication-provider-aad).
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
