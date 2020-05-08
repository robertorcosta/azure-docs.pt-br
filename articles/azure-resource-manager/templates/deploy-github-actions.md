---
title: Implantar modelos do Resource Manager usando ações do GitHub
description: Descreve como implantar modelos de Azure Resource Manager usando ações do GitHub.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875188"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Implantar modelos de Azure Resource Manager usando ações do GitHub

As [ações do GitHub](https://help.github.com/en/actions) permitem que você crie fluxos de trabalho de ciclo de vida de desenvolvimento de software personalizados diretamente no repositório GitHub onde seus modelos de Azure Resource Manager (ARM) são armazenados. Um [fluxo de trabalho](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) é definido por um arquivo YAML. Os fluxos de trabalho têm um ou mais trabalhos que contêm um conjunto de etapas que executam tarefas individuais. As etapas podem executar comandos ou usar uma ação. Você pode criar suas próprias ações ou usar ações compartilhadas pela [comunidade do GitHub](https://github.com/marketplace?type=actions) e personalizá-las conforme necessário. Este artigo mostra como usar [CLI do Azure ação](https://github.com/marketplace/actions/azure-cli-action) para implantar modelos do Resource Manager.

CLI do Azure ação tem duas ações dependentes:

- **[Check-](https://github.com/marketplace/actions/checkout)** out: Confira o repositório para que o fluxo de trabalho possa acessar qualquer modelo do Resource Manager especificado.
- **[Logon do Azure](https://github.com/marketplace/actions/azure-login)**: faça logon com suas credenciais do Azure

Um fluxo de trabalho básico para implantar um modelo do Resource Manager pode ter três etapas:

1. Confira um arquivo de modelo.
2. Entre no Azure.
3. Implantar um modelo do Resource Manager

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um repositório GitHub para armazenar seus modelos do Resource Manager e seus arquivos de fluxo de trabalho. Para criar um, consulte [criando um novo repositório](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>configurar credenciais de implantação

A ação de logon do Azure usa uma entidade de serviço para se autenticar no Azure. O principal de um fluxo de trabalho de CI/CD normalmente precisa do direito de colaborador interno para implantar recursos do Azure.

O script de CLI do Azure a seguir mostra como gerar uma entidade de serviço do Azure com permissões de colaborador em um grupo de recursos do Azure. Esse grupo de recursos é onde o fluxo de trabalho implanta os recursos definidos em seu modelo do Resource Manager.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalize o valor de **$projectName** e **$Location** no script. O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**. Você precisa especificar o nome do grupo de recursos em seu fluxo de trabalho.

O script gera um objeto JSON semelhante a este:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Copie a saída JSON e armazene-a como um segredo do GitHub no repositório GitHub. Consulte [pré-requisito](#prerequisites) se você ainda não tiver um repositório.

1. No repositório GitHub, selecione a guia **configurações** .
1. Selecione **segredo** no menu à esquerda.
1. Insira os valores a seguir:

    - **Nome**: AZURE_CREDENTIALS
    - **Valor**: (Cole a saída JSON)
1. Selecione **Adicionar segredo**.

Você precisa especificar o nome do segredo no fluxo de trabalho.

## <a name="add-resource-manager-template"></a>Adicionar modelo do Resource Manager

Adicione um modelo do Resource Manager ao repositório do GitHub. Se você não tiver um, poderá usar o modelo a seguir. O modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Você pode colocar o arquivo em qualquer lugar no repositório. O exemplo de fluxo de trabalho na próxima seção pressupõe que o arquivo de modelo é denominado **azuredeploy. JSON**e é armazenado em uma pasta chamada **modelos** na raiz do seu repositório.

## <a name="create-workflow"></a>Criar fluxo de trabalho

O arquivo de fluxo de trabalho deve ser armazenado na pasta **. github/Workflow** na raiz do seu repositório. A extensão do arquivo de fluxo de trabalho pode ser **. yml** ou **. YAML**.

Você pode criar um arquivo de fluxo de trabalho e, em seguida, enviar/carregar o arquivo para o repositório ou usar o seguinte procedimento:

1. No repositório GitHub, selecione **ações** no menu superior.
1. Selecione **novo fluxo de trabalho**.
1. Selecione **configurar um fluxo de trabalho por conta própria**.
1. Renomeie o arquivo de fluxo de trabalho se você preferir outro nome diferente de **Main. yml**. Por exemplo: **deployStorageAccount. yml**.
1. Substitua o conteúdo do arquivo yml pelo seguinte:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    O arquivo de fluxo de trabalho tem três seções:

    - **nome**: o nome do fluxo de trabalho.
    - **em**: o nome dos eventos do GitHub que dispara o fluxo de trabalho. O fluxo de trabalho é disparado quando há um evento de push no Branch mestre, que modifica pelo menos um dos dois arquivos especificados. Os dois arquivos são o arquivo de fluxo de trabalho e o arquivo de modelo.

        > [!IMPORTANT]
        > Verifique se os dois arquivos e seus caminhos correspondem aos seus.
    - **trabalhos**: uma execução de fluxo de trabalho é composta de um ou mais trabalhos. Há apenas um trabalho chamado **Deploy-Storage-Account-template**.  Esse trabalho tem três etapas:

        - **Código-fonte de check-out**.
        - **Faça logon no Azure**.

            > [!IMPORTANT]
            > Verifique se o nome do segredo corresponde ao que você salvou em seu repositório. Consulte [configurar credenciais de implantação](#configure-deployment-credentials).
        - **Implante o modelo ARM**. Substitua o valor de **resourceGroupName**.  Se você usou o script CLI do Azure em [configurar credenciais de implantação](#configure-deployment-credentials), o nome do grupo de recursos gerado será o nome do projeto com **RG** acrescentado. Verifique o valor de **templateLocation**.

1. Selecione **Iniciar confirmação**.
1. Selecione **confirmar diretamente no Branch mestre**.
1. Selecione **confirmar novo arquivo** (ou **confirmar alterações**).

Como o fluxo de trabalho está configurado para ser disparado pelo arquivo de fluxo de trabalho ou pelo arquivo de modelo que está sendo atualizado, o fluxo de trabalho é iniciado logo após a confirmação das alterações.

## <a name="check-workflow-status"></a>Verificar status do fluxo de trabalho

1. Selecione a guia **ações** . Você deverá ver um fluxo de trabalho **criar deployStorageAccount. yml** listado. Leva 1-2 minutos para executar o fluxo de trabalho.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione **implantar-armazenamento-conta-modelo** (nome do trabalho) no menu à esquerda. O nome do trabalho é definido no Workflow.
1. Selecione **implantar modelo ARM** (nome da etapa) para expandi-lo. Você pode ver a resposta da API REST.

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, consulte [tutorial: criar e implantar seu primeiro modelo de ARM](template-tutorial-create-first-template.md).
