---
title: Implantar modelos do Resource Manager usando GitHub Actions
description: Descreve como implantar modelos do Azure Resource Manager usando GitHub Actions.
ms.topic: conceptual
ms.date: 07/02/2020
ms.custom: github-actions-azure
ms.openlocfilehash: cea099088005fa91e1b3e9a793105df4796a66ee
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018569"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Implantar modelos do Azure Resource Manager usando GitHub Actions

O recurso [GitHub Actions](https://help.github.com/en/actions) permite criar fluxos de trabalho personalizados do ciclo de vida de desenvolvimento de software diretamente no repositório GitHub, local em que estão armazenados os modelos do ARM (Azure Resource Manager). Um [fluxo de trabalho](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) é definido por um arquivo YAML. Em cada trabalho, os fluxos de trabalho têm um ou mais trabalhos com um conjunto de etapas que executam tarefas individuais. As etapas podem executar comandos ou usar uma ação. Você pode criar suas ações ou usar ações compartilhadas pela comunidade do [GitHub](https://github.com/marketplace?type=actions) e personalizá-las conforme necessário. Este artigo mostra como usar a [Ação da CLI do Azure](https://github.com/marketplace/actions/azure-cli-action) para implantar modelos do Resource Manager.

A ação da CLI do Azure tem duas ações dependentes:

- **[Fazer checkout](https://github.com/marketplace/actions/checkout)** : confira seu repositório para que o fluxo de trabalho possa acessar qualquer modelo especificado do Resource Manager.
- **[Logon do Azure](https://github.com/marketplace/actions/azure-login)** : entre com suas credenciais do Azure

Um fluxo de trabalho básico para implantar um modelo do Resource Manager pode ter três etapas:

1. Fazer check-out de um arquivo de modelo.
2. Entre no Azure.
3. Implantar um modelo do Azure Resource Manager

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter um repositório GitHub para armazenar seus modelos do Resource Manager e seus arquivos de fluxo de trabalho. Para criá-lo, confira [Como criar um repositório](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>configurar credenciais de implantação

A ação de logon do Azure usa uma entidade de serviço de autenticação no Azure. A entidade de segurança de um fluxo de trabalho de CI/CD normalmente precisa de direitos de colaborador interno atribuídos para implantar recursos do Azure.

O script de CLI do Azure a seguir mostra como gerar uma Entidade de Serviço do Azure com permissões de Colaborador em um grupo de recursos do Azure. Esse grupo de recursos é o local em que o fluxo de trabalho implanta os recursos definidos no seu modelo do Resource Manager.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalize o valor de **$projectName** e **$location** no script. O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**. Você precisa especificar o nome do grupo de recursos no seu fluxo de trabalho.

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

Copie o JSON gerado e armazene-o como um segredo dentro do seu repositório GitHub. Se você ainda não tem um repositório, confira [Pré-requisitos](#prerequisites).

1. No repositório GitHub, selecione a guia **Configurações**.
1. Selecione **Segredos** no painel esquerdo.
1. Insira os valores a seguir:

    - **Name**: AZURE_CREDENTIALS
    - **Valor**: (Copie o JSON gerado)
1. Selecione **Adicionar segredo**.

Você precisa especificar o nome do segredo no fluxo de trabalho.

## <a name="add-resource-manager-template"></a>Adicionar modelo do Resource Manager

Adicione um modelo do Resource Manager ao repositório GitHub. Se você não tem um, use modelo a seguir. O modelo cria uma conta de armazenamento.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Você pode colocar o arquivo em qualquer lugar do repositório. O exemplo do fluxo de trabalho na próxima seção presume que o arquivo de modelo foi denominado como **azuredeploy.json** e armazenado em uma pasta chamada **modelos** na raiz do seu repositório.

## <a name="create-workflow"></a>Criar fluxo de trabalho

O arquivo de fluxo de trabalho deve ser armazenado na pasta **. github/fluxos de trabalho** na raiz do seu repositório. A extensão do arquivo do fluxo de trabalho pode ser **.yml** ou **.yaml**.

Você pode criar um arquivo de fluxo de trabalho e efetuar push/carregar o arquivo para o repositório ou usar o seguinte procedimento:

1. No repositório GitHub, selecione **Actions** no menu superior.
1. Selecione **Novo fluxo de trabalho**.
1. Selecione **Configurar fluxo de trabalho por conta própria**.
1. Renomeie o arquivo do fluxo de trabalho se preferir usar um nome diferente de **main.yml**. Por exemplo: **deployStorageAccount.yml**.
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

    O arquivo do fluxo de trabalho possui três seções:

    - **nome**: Nome do fluxo de trabalho.
    - **Ativado**: o nome dos eventos do GitHub que acionam o fluxo de trabalho. O fluxo de trabalho é acionado quando há um evento no qual efetua-se o push na ramificação mestre, que modifica pelo menos um dos dois arquivos especificados. Os dois arquivos correspondem ao arquivo de fluxo de trabalho e ao arquivo de modelo.

        > [!IMPORTANT]
        > Verifique os dois arquivos e seus caminhos correspondem aos que você possui.
    - **Trabalhos**: uma execução do fluxo de trabalho é composta por um ou mais trabalhos. Há apenas um trabalho chamado **deploy-storage-account-template**.  Esse trabalho tem três etapas:

        - **Checkout do código-fonte**.
        - **Logon no azure**.

            > [!IMPORTANT]
            > Verifique se o nome do segredo corresponde ao que você salvou no repositório. Confira [Configurar credenciais de implantação](#configure-deployment-credentials).
        - **Implantar modelo do ARM**. Substitua o valor de **resourceGroupName**.  Se você usou o script da CLI do Azure em [Configurar credenciais de implantação](#configure-deployment-credentials), o nome do grupo de recursos gerado será o nome do projeto com **rg** anexado. Verifique o valor de **templateLocation**.

1. Selecione **Confirmar início**.
1. Selecione **Confirmar diretamente na ramificação mestre**.
1. Selecione **Confirmar novo arquivo** (ou **Confirmar alterações**).

Como o fluxo de trabalho está configurado para ser acionado pelo arquivo de fluxo de trabalho ou pelo arquivo de modelo que está sendo atualizado, ele é iniciado logo após você confirmar as alterações.

## <a name="check-workflow-status"></a>Verificar status do fluxo de trabalho

1. Selecione a guia **Actions**. Você verá listado um fluxo de trabalho **Criar deployStorageAccount.yml**. O fluxo de trabalho leva de um a dois minutos para ser executado.
1. Selecione o fluxo de trabalho para abri-lo.
1. Selecione **deploy-storage-account-template** (nome do trabalho) no menu à esquerda. O nome do trabalho é definido no fluxo de trabalho.
1. Selecione **Implantar modelo do ARM** (nome da etapa) para expandi-lo. Você verá a resposta da API REST.

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira [Tutorial: criar e implantar seu primeiro modelo do ARM](template-tutorial-create-first-template.md).
