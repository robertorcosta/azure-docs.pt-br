---
title: Conecte-se ao Azure Media Services v3 API - Python
description: Este artigo demonstra como se conectar à API v3 do Media Services com python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888454"
---
# <a name="connect-to-media-services-v3-api---python"></a>Conecte-se aos Serviços de Mídia v3 API - Python

Este artigo mostra como se conectar ao Azure Media Services v3 Python SDK usando o método de login principal do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- Baixe Python de [python.org](https://www.python.org/downloads/)
- Certifique-se de `PATH` definir a variável de ambiente
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Mídia.
- Siga as etapas no tópico APIs de [acesso.](access-api-cli-how-to.md) Registre o ID de assinatura, o ID do aplicativo (ID do cliente), a chave de autenticação (secreta) e o ID do inquilino que você precisa na etapa posterior.

> [!IMPORTANT]
> Revisar [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Azure Media Services usando python, você precisa instalar esses módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos Azure para Active Directory.
* O `azure-mgmt-media` módulo, que inclui as entidades de Serviços de Mídia.

Abra uma ferramenta de linha de comando e use os seguintes comandos para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Conecte-se ao cliente Python

1. Criar um arquivo `.py` com uma extensão
1. Abra o arquivo em seu editor favorito
1. Adicione o código que segue ao arquivo. O código importa os módulos necessários e cria o objeto de credenciais do Active Directory que você precisa para se conectar aos Serviços de Mídia.

      Defina os valores das variáveis para os valores que você tem das [APIs](access-api-cli-how-to.md) de acesso

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Execute o arquivo

## <a name="next-steps"></a>Próximas etapas

- Use o [SDK do Python](https://aka.ms/ams-v3-python-sdk).
- Examine a documentação de [referência do Python](https://aka.ms/ams-v3-python-ref) dos Serviços de Mídia.
