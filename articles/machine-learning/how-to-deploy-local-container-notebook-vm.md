---
title: Como implantar modelos para calcular instâncias
titleSuffix: Azure Machine Learning
description: Aprenda a implantar seus modelos de Machine Learning do Azure como um serviço web usando instâncias computacionais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398150"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Implantar um modelo para as instâncias de cálculo do Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar o Azure Machine Learning para implantar um modelo como um serviço web em sua instância de computação de Machine Learning do Azure. Use instâncias computacionais se uma das seguintes condições for verdadeira:

- Você precisa implantar rapidamente e validar o modelo.
- Você está testando um modelo ainda em desenvolvimento.

> [!TIP]
> Implantar um modelo de um Notebook Jupyter em uma instância de computação, para um serviço web na mesma VM é uma _implantação local_. Neste caso, o computador 'local' é a instância de computação. Para obter mais informações sobre implantações, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho de aprendizado de máquina do Azure com uma instância computacional em execução. Para obter mais informações, consulte [o ambiente de configuração e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Implantar nas instâncias de computação

Um notebook de exemplo que demonstra implantações locais está incluído na sua instância de computação. Use as seguintes etapas para carregar o notebook e implantar o modelo como um serviço web na VM:

1. No [estúdio Azure Machine Learning,](https://ml.azure.com)selecione as instâncias de cálculo do Azure Machine Learning.

1. Abra `samples-*` o subdiretório e `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`abra. Uma vez aberto, execute o caderno.

    ![Captura de tela do serviço local em execução no notebook](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. O notebook exibe a URL e a porta em que o serviço está sendo executado. Por exemplo, `https://localhost:6789`. Você também pode executar `print('Local service port: {}'.format(local_service.port))` a célula que contém para exibir a porta.

    ![Captura de tela da porta de serviço local em execução](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Para testar o serviço a partir `https://localhost:<local_service.port>` de uma instância computacional, use a URL. Para testar a partir de um cliente remoto, obtenha a URL pública do serviço em execução na instância de computação. A URL pública pode ser determinada usando a seguinte fórmula; 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Exemplo de `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`computação: . 

    Por exemplo, 
    * Notebook VM:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Exemplo de cálculo:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Teste o serviço

Para enviar dados de amostra para o serviço em execução, use o seguinte código. Substitua o `service_url` valor de com a URL da etapa anterior:

> [!NOTE]
> Ao autenticar uma implantação na instância de computação, a autenticação é feita usando o Azure Active Directory. A chamada `interactive_auth.get_authentication_header()` no código de exemplo autentica você usando AAD e retorna um cabeçalho que pode ser usado para autenticar o serviço na instância de computação. Para obter mais informações, consulte [Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning](how-to-setup-authentication.md#interactive-authentication).
>
> Ao autenticar uma implantação no Azure Kubernetes Service ou no Azure Container Instances, um método de autenticação diferente é usado. Para obter mais informações, consulte [Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)