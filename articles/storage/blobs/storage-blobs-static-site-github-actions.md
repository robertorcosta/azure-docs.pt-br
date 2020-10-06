---
title: Usar ações do GitHub para implantar um site estático no armazenamento do Azure
description: Hospedagem de sites estáticos do armazenamento do Azure com ações do GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 9b616f8df0a8b64969fac1d18544e6d316120e50
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771023"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Configurar um fluxo de trabalho de ações do GitHub para implantar seu site estático no armazenamento do Azure

Introdução às [ações do GitHub](https://docs.github.com/en/actions) usando um fluxo de trabalho para implantar um site estático em um blob de armazenamento do Azure. Depois de configurar um fluxo de trabalho de ações do GitHub, você poderá implantar automaticamente seu site no Azure do GitHub quando fizer alterações no código do site. 

> [!NOTE]
> Se você estiver usando [aplicativos Web estáticos do Azure](https://docs.microsoft.com/azure/static-web-apps/), não será necessário configurar manualmente um fluxo de trabalho de ações do github.
> Os aplicativos Web estáticos do Azure criam automaticamente um fluxo de trabalho do GitHub para você. 

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure e uma conta do GitHub. 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma conta do GitHub com seu código de site estático. Se você não tiver uma conta do GitHub, [Inscreva-se gratuitamente](https://github.com/join).  
- Um site estático funcional hospedado no armazenamento do Azure. Saiba como [hospedar um site estático no armazenamento do Azure](storage-blob-static-website-how-to.md). Seu site estático deve incluir uma [CDN do Azure](static-website-content-delivery-network.md).

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Você pode criar uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) na [CLI do Azure](/cli/azure/). Execute este comando com [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

Substitua o espaço reservado `myStaticSite` pelo nome do seu site hospedado no armazenamento do Azure. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

No exemplo acima, substitua os espaços reservados por sua ID de assinatura e o nome do grupo de recursos. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo do serviço de aplicativo semelhante ao mostrado abaixo. Copie este objeto JSON para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

1. No [GitHub](https://github.com/), procure seu repositório.

1. Selecione **configurações > segredos > novo segredo**.

1. Cole toda a saída JSON do comando CLI do Azure no campo valor do segredo. Dê ao segredo o nome como `AZURE_CREDENTIALS` .

    Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para a entrada `creds` da ação de logon do Azure. Por exemplo:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Adicionar seu fluxo de trabalho

1. Vá para **ações** para seu repositório github. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Item de menu de ações do GitHub&quot;:::

1. Selecione **configurar seu fluxo de trabalho por conta própria**. 

1. Exclua tudo após a `on:` seção do seu arquivo de fluxo de trabalho. Por exemplo, o fluxo de trabalho restante pode ter a seguinte aparência. 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renomeie o fluxo de trabalho `Blob storage website CI` e adicione as ações de check-out e logon. Essas ações efetuarão checkout do código do site e serão autenticadas com o Azure usando o `AZURE_CREDENTIALS` segredo do GitHub criado anteriormente. 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Use a ação CLI do Azure para carregar seu código no armazenamento de BLOBs e para limpar o ponto de extremidade da CDN. Para `az storage blob upload-batch` , substitua o espaço reservado pelo nome da conta de armazenamento. O script será carregado no `$web` contêiner. Para `az cdn endpoint purge` , substitua os espaços reservados pelo nome do perfil CDN, pelo nome do ponto de extremidade da CDN e pelo grupo de recursos.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Conclua o fluxo de trabalho adicionando uma ação para fazer logoff do Azure. Este é o fluxo de trabalho concluído. O arquivo aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Examine sua implantação

1. Vá para **ações** para seu repositório github. 

1. Abra o primeiro resultado para ver os logs detalhados da execução do fluxo de trabalho. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Item de menu de ações do GitHub&quot;:::

1. Selecione **configurar seu fluxo de trabalho por conta própria**. 

1. Exclua tudo após a `on:` seção do seu arquivo de fluxo de trabalho. Por exemplo, o fluxo de trabalho restante pode ter a seguinte aparência. 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renomeie o fluxo de trabalho `Blob storage website CI` e adicione as ações de check-out e logon. Essas ações efetuarão checkout do código do site e serão autenticadas com o Azure usando o `AZURE_CREDENTIALS` segredo do GitHub criado anteriormente. 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Use a ação CLI do Azure para carregar seu código no armazenamento de BLOBs e para limpar o ponto de extremidade da CDN. Para `az storage blob upload-batch` , substitua o espaço reservado pelo nome da conta de armazenamento. O script será carregado no `$web` contêiner. Para `az cdn endpoint purge` , substitua os espaços reservados pelo nome do perfil CDN, pelo nome do ponto de extremidade da CDN e pelo grupo de recursos.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Conclua o fluxo de trabalho adicionando uma ação para fazer logoff do Azure. Este é o fluxo de trabalho concluído. O arquivo aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando seu site estático do Azure e o repositório não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório do GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os aplicativos Web estáticos do Azure](https://docs.microsoft.com/azure/static-web-apps/)