---
title: Usar o Visual Studio Code para criar modelos do Azure Resource Manager | Microsoft Docs
description: Use a extensão de ferramentas do Visual Studio Code e do Azure Resource Manager para trabalhar em modelos do Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: b62d4f8b43c62ff401b4d74932b131c1bf8fd63f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169572"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Início Rápido: Criar modelos do Azure Resource Manager usando o Visual Studio Code

Saiba como usar o Visual Studio Code e a extensão de Ferramentas do Azure Resource Manager para criar e editar modelos do Azure Resource Manager. Você pode criar modelos do Gerenciador de Recursos no Visual Studio Code sem a extensão, mas a extensão fornece opções de preenchimento automático que simplificam o desenvolvimento do modelo. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).

Neste início rápido, você implantará uma conta de armazenamento:

![diagrama de início rápido do Visual Studio Code do modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

- [Visual Studio Code](https://code.visualstudio.com/).
- Extensão das Ferramentas do Gerenciador de Recursos. Para instalar, use estas etapas:

    1. Abra o Visual Studio Code.
    2. Pressione **CTRL+SHIFT+X** para abrir o painel Extensões
    3. Procure **Ferramentas do Azure Resource Manager** e escolha **Instalar**.
    4. Para concluir a instalação da extensão, escolha **Recarregar**.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo do zero, você pode abrir um modelo de [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Modelos de Início Rápido do Azure é um repositório de modelos do Resource Manager.

O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.
4. Escolha **Arquivo**>**Salvar como** para salvar o arquivo como **azuredeploy.json** em seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

Para experimentar como editar um modelo usando o Visual Studio Code, adicione mais um elemento à seção `outputs` para mostrar o URI de armazenamento.

1. Adicione mais uma saída ao modelo exportado:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Quando terminar, a seção de saídas terá essa aparência:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Se você tiver copiado e colado o código dentro do Visual Studio Code, tente digitar novamente o elemento **value** para experimentar a funcionalidade do IntelliSense da extensão das Ferramentas do Resource Manager.

    ![IntelliSense do Visual Studio Code do modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Escolha **Arquivo**>**Salvar** para salvar o arquivo.

## <a name="deploy-the-template"></a>Implantar o modelo

Há muitos métodos para implantar modelos. O Azure Cloud Shell é usado neste início rápido. O Cloud Shell oferece suporte à CLI do Azure e o Azure PowerShell. Use o seletor de guias para escolher entre o CLI e PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

2. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (CLI) no canto superior esquerdo.  Ao alternar, é necessário reiniciar o shell.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![CLI do Cloud Shell no portal do Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![PowerShell do Cloud Shell no portal do Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Selecione o arquivo que você salvou na seção anterior. O nome padrão é **azuredeploy.json**. O arquivo de modelo precisa estar acessível por meio do shell.

    Opcionalmente, você pode usar o comando **ls** e o comando **cat** para verificar se o arquivo foi carregado com êxito.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. No Cloud Shell, execute os seguintes comandos. Selecione a guia para mostrar o código do PowerShell ou o código da CLI.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Atualize o nome do arquivo de modelo se você salvá-lo com um nome diferente de **azuredeploy.json**.

    A captura de tela a seguir mostra uma implantação de exemplo:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Cloud Shell no portal do Azure implantar modelo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Cloud Shell no portal do Azure implantar modelo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    O nome da conta de armazenamento e a URL de armazenamento na seção de saídas estão realçadas na captura de tela. Você precisa do nome da conta de armazenamento na próxima etapa.

5. Execute o seguinte comando da CLI ou do PowerShell para listar as contas de armazenamento criadas recentemente:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Para saber mais sobre como usar as contas de armazenamento do Azure, confira [Início rápido: carregar, baixar e listar blobs usando o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

O foco principal deste início rápido é usar o Visual Studio Code para editar um modelo existente a partir de modelos de Início Rápido do Azure. Você também aprendeu a implantar o modelo usando a CLI ou o PowerShell por meio do Azure Cloud Shell. Os modelos do Início Rápido do Azure podem não oferecer tudo o que você precisa. Para saber mais sobre o desenvolvimento de modelos, confira nossa nova série de tutoriais para iniciantes:

> [!div class="nextstepaction"]
> [Tutoriais para iniciante](./template-tutorial-create-first-template.md)
