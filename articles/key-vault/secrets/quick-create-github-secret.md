---
title: Guia de Início Rápido – Usar segredos do Azure Key Vault em fluxos de trabalho do GitHub Actions
description: Use segredos do Key Vault no GitHub Actions para automatizar seus fluxos de trabalho de desenvolvimento de software
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: c1e8062210de1d7d99f57a4e0b155492f7dfdd9a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785969"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Usar segredos do Key Vault em fluxos de trabalho do GitHub Actions

Use segredos do Key Vault no [GitHub Actions](https://help.github.com/en/articles/about-github-actions) e armazene senhas com segurança e outros segredos em um cofre de chaves do Azure. Saiba mais sobre o [Key Vault](../general/overview.md). 

Com o Key Vault e o GitHub Actions, você obtém os benefícios de uma ferramenta de gerenciamento centralizado de segredos e todas as vantagens do GitHub Actions. O GitHub Actions são um conjunto de recursos do GitHub usados para automatizar seus fluxos de trabalho de desenvolvimento de software. Você pode implantar fluxos de trabalho no mesmo local em que armazena código e colabora em solicitações de pull e problemas. 


## <a name="prerequisites"></a>Pré-requisitos 
- Uma conta do GitHub. Caso ainda não tenha uma, inscreva-se [gratuitamente](https://github.com/join).  
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Azure App conectado a um repositório GitHub. Este exemplo usa [Implantar contêineres no Serviço de Aplicativo do Azure](/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Um cofre de chaves do Azure.  Crie um Azure Key Vault usando o portal do Azure, a CLI do Azure ou o Azure PowerShell.

## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

O arquivo tem duas seções para autenticação no GitHub Actions:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Definir uma entidade de serviço. <br /> 2. Criar um segredo do GitHub. <br /> 3. Adicionar uma atribuição de função. |
|**Key Vault** | 1. Adicionar a ação do cofre de chaves. <br /> 2. Referenciar o segredo do cofre de chaves. |

## <a name="authentication"></a>Autenticação

Crie uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

No exemplo acima, substitua os espaços reservados pela sua ID da assinatura e o nome do grupo de recursos. Substitua o espaço reservado `myApp` pelo nome do aplicativo. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao aplicativo do Serviço de Aplicativo semelhante ao mostrado abaixo. Copie esse objeto JSON para uso posterior. Você só precisará das seções com os valores `clientId`, `clientSecret`, `subscriptionId` e `tenantId`. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

Crie segredos para suas credenciais, seu grupo de recursos e suas assinaturas do Azure. 

1. Em [GitHub](https://github.com/), procure seu repositório.

1. Selecione **Configurações > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS`.

1. Copie o valor de `clientId` para uso posterior. 

### <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função 
 
Você precisará permitir acesso à entidade de serviço do Azure a fim de acessar o cofre de chaves para as operações `get` e `list`. Se você não fizer isso, não poderá usar a entidade de serviço. 

Substitua `keyVaultName` pelo nome do cofre de chaves e `clientIdGUID` pelo valor da sua `clientId`. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Usar a ação do cofre de chaves do Azure

Com a [ação do cofre de chaves do Azure](https://github.com/marketplace/actions/azure-key-vault-get-secrets), você poderá buscar um ou mais segredos de uma instância do cofre de chaves do Azure e consumi-los nos seus fluxos de trabalho do GitHub Actions.

Os segredos buscados são definidos como saídas e também como variáveis de ambiente. As variáveis são mascaradas automaticamente quando são impressas no console ou nos logs.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Para usar um cofre de chaves no seu fluxo de trabalho, você precisará da ação do cofre de chaves e referenciar essa ação. 

Neste exemplo, o cofre de chaves é chamado `containervault`. Dois segredos do cofre de chaves são adicionados ao ambiente com a ação do cofre de chaves: `containerPassword` e `containerUsername`. 

Os valores do cofre de chaves são referenciados posteriormente na tarefa de logon do Docker com o prefixo `steps.myGetSecretAction.outputs`. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando seu aplicativo do Azure, o repositório GitHub e o cofre de chaves não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos do aplicativo, o repositório GitHub e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Key Vault](../general/overview.md)