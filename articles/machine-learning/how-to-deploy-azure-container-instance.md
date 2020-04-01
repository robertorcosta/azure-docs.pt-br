---
title: Como implantar modelos em Instâncias de Contêineres do Azure
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Machine Learning do Azure como um serviço web usando instâncias de contêiner do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475466"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Implantar um modelo nas Instâncias de Contêiner do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar o Azure Machine Learning para implantar um modelo como serviço web no Azure Container Instances (ACI). Use as instâncias do contêiner Azure se uma das seguintes condições for verdadeira:

- Você precisa implantar rapidamente e validar o modelo. Você não precisa criar contêineres ACI antes do tempo. Eles são criados como parte do processo de implantação.
- Você está testando um modelo ainda em desenvolvimento. 

Para obter informações sobre a disponibilidade de cotas e região para ACI, consulte Cotas e disponibilidade de região para o artigo [Azure Container Instances.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tem um modelo registrado, veja [Como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão Azure CLI para serviço de aprendizado de máquina,](reference-azure-machine-learning-cli.md) [O Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Os trechos de código __Python__ neste artigo assumem que as seguintes variáveis estão definidas:

    * `ws`- Coloque-se no seu espaço de trabalho.
    * `model`- Ajuste para o seu modelo registrado.
    * `inference_config`- Definir a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição dessas variáveis, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos da __CLI__ neste artigo supõem `inferenceconfig.json` que você criou um documento. Para obter mais informações sobre a criação deste documento, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Implantar no ACI

Para implantar um modelo no Azure Container Instances, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência,__ que descreva o ambiente necessário para hospedar o modelo e o serviço web. Para obter mais informações sobre como criar a configuração de inferência, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando o CLI, use o seguinte comando. Substitua pelo `mymodel:1` nome e versão do modelo registrado. Substitua pelo `myservice` nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Para obter mais informações, consulte [o modelo az ml implantar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referência. 

## <a name="using-vs-code"></a>Usar o VS Code

Veja [implantar seus modelos com código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Você não precisa criar um contêiner ACI para testar com antecedência. Os contêineres ACI são criados conforme necessário.

## <a name="update-the-web-service"></a>Atualizar o serviço Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
