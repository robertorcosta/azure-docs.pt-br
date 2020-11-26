---
title: Implantar modelos do Resource Manager usando GitHub Actions
description: Descreve como implantar modelos do Azure Resource Manager usando GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: cf705f68544c4c4e0db55d4a375e1e50530c8957
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185701"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Implantar modelos do Azure Resource Manager usando GitHub Actions

As [ações do GitHub](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) são um conjunto de recursos do GitHub para automatizar seus fluxos de trabalho de desenvolvimento de software no mesmo local em que você armazena código e colabora em solicitações de pull e problemas.

Use a [ação implantar modelo de Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) para automatizar a implantação de um modelo do Resource Manager no Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma conta do GitHub. Caso ainda não tenha uma, inscreva-se [gratuitamente](https://github.com/join).  
    - Um repositório GitHub para armazenar seus modelos do Resource Manager e seus arquivos de fluxo de trabalho. Para criá-lo, confira [Como criar um repositório](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

O arquivo tem duas seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Definir uma entidade de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implantar** | 1. implante o modelo do Resource Manager. |

## <a name="generate-deployment-credentials"></a>Gerar as credenciais de implantação


Crie uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

Crie um grupo de recursos se você ainda não tiver um. 

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

Substitua o espaço reservado `myApp` pelo nome do seu aplicativo. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

No exemplo acima, substitua os espaços reservados por sua ID de assinatura e o nome do grupo de recursos. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo do serviço de aplicativo semelhante ao mostrado abaixo. Copie este objeto JSON para mais tarde. Você só precisará das seções com os `clientId` `clientSecret` valores,, `subscriptionId` e `tenantId` . 

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
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao grupo de recursos.



## <a name="configure-the-github-secrets"></a>Configurar os segredos do GitHub

Você precisa criar segredos para suas credenciais, grupo de recursos e assinaturas do Azure. 

1. Em [GitHub](https://github.com/), procure seu repositório.

1. Selecione **Configurações > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS`.

1. Crie outro segredo chamado `AZURE_RG` . Adicione o nome do grupo de recursos ao campo valor do segredo (exemplo: `myResourceGroup` ). 

1. Crie um segredo adicional chamado `AZURE_SUBSCRIPTION` . Adicione sua ID de assinatura ao campo valor do segredo (exemplo: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ). 

## <a name="add-resource-manager-template"></a>Adicionar modelo do Resource Manager

Adicione um modelo do Resource Manager ao repositório GitHub. Este modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Você pode colocar o arquivo em qualquer lugar do repositório. O exemplo de fluxo de trabalho na próxima seção pressupõe que o arquivo de modelo é nomeado **azuredeploy.jsem** e é armazenado na raiz do seu repositório.

## <a name="create-workflow"></a>Criar fluxo de trabalho

O arquivo de fluxo de trabalho deve ser armazenado na pasta **. github/fluxos de trabalho** na raiz do seu repositório. A extensão do arquivo do fluxo de trabalho pode ser **.yml** ou **.yaml**.

1. No repositório GitHub, selecione **Actions** no menu superior.
1. Selecione **Novo fluxo de trabalho**.
1. Selecione **Configurar fluxo de trabalho por conta própria**.
1. Renomeie o arquivo do fluxo de trabalho se preferir usar um nome diferente de **main.yml**. Por exemplo: **deployStorageAccount.yml**.
1. Substitua o conteúdo do arquivo yml pelo seguinte:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS 
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Você pode especificar um arquivo de parâmetros de formato JSON na ação de implantação do ARM (exemplo: `.azuredeploy.parameters.json` ).  

    A primeira seção do arquivo de fluxo de trabalho inclui:

    - **nome**: Nome do fluxo de trabalho.
    - **Ativado**: o nome dos eventos do GitHub que acionam o fluxo de trabalho. O fluxo de trabalho é disparado quando há um evento de push na ramificação principal, que modifica pelo menos um dos dois arquivos especificados. Os dois arquivos correspondem ao arquivo de fluxo de trabalho e ao arquivo de modelo.

1. Selecione **Confirmar início**.
1. Selecione **confirmar diretamente na ramificação principal**.
1. Selecione **Confirmar novo arquivo** (ou **Confirmar alterações**).

Como o fluxo de trabalho está configurado para ser acionado pelo arquivo de fluxo de trabalho ou pelo arquivo de modelo que está sendo atualizado, ele é iniciado logo após você confirmar as alterações.

## <a name="check-workflow-status"></a>Verificar status do fluxo de trabalho

1. Selecione a guia **ações** . Você verá um fluxo de trabalho **criar deployStorageAccount. yml** listado. Leva 1-2 minutos para executar o fluxo de trabalho.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione **executar o ARM implantar** no menu para verificar a implantação.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando o grupo de recursos e o repositório não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar seu primeiro modelo de ARM](./template-tutorial-create-first-template.md)