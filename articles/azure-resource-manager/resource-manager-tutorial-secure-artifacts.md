---
title: Proteger artefatos em modelos
description: Saiba como proteger os artefatos usados em seus modelos do Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a9d209e843d8e9a1735a3c6907b00d85be6580b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971717"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Proteger os artefatos em implantações de modelo do Azure Resource Manager

Saiba como proteger os artefatos usados em modelos do Azure Resource Manager usando a conta de Armazenamento do Azure com SAS (assinaturas de acesso compartilhado). Artefatos de implantação são quaisquer arquivos, além do arquivo de modelo principal, necessários para concluir uma implantação. Por exemplo, no [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), o modelo principal cria um Banco de Dados SQL do Azure; ele também chama um arquivo BACPAC para criar tabelas e inserir dados. O arquivo BACPAC é um artefato armazenado em uma conta de armazenamento do Azure. A chave de conta de armazenamento foi usada para acessar o artefato. Neste tutorial, você pode usar SAS para conceder acesso limitado ao arquivo BACPAC em sua própria conta de Armazenamento do Azure. Para saber mais sobre SAS, confira [Usar SAS (assinaturas de acesso compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para saber como proteger o modelo vinculado, confira [Tutorial: Criar modelos do Azure Resource Manager vinculados](./resource-manager-tutorial-create-linked-templates.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um arquivo BACPAC
> * Abrir um modelo existente
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Examine [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). O modelo usado neste tutorial é o desenvolvido naquele tutorial. Um link de download do modelo concluído é fornecido neste artigo.
* Para aumentar a segurança, use uma senha gerada para a conta de administrador do SQL Server. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um arquivo BACPAC

Nesta seção, você deve preparar um arquivo BACPAC para que o arquivo esteja acessível com segurança quando você implantar o modelo do Resource Manager. Há cinco procedimentos nesta seção:

* Baixe o arquivo BACPAC.
* Criar uma conta do Armazenamento do Azure.
* Crie um contêiner de blobs da conta de armazenamento.
* Carregue o arquivo BACPAC para o contêiner.
* Recupere o token SAS do arquivo BACPAC.

1. Selecione **Experimentar** para abrir o Cloud Shell e, em seguida, cole o script do PowerShell a seguir na janela do Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Anote a URL do arquivo BACPAC e o token SAS. Você precisará desses valores ao implantar o modelo.

## <a name="open-an-existing-template"></a>Abrir um modelo existente

Nesta sessão, você modifica o modelo criado no [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) para chamar o arquivo BACPAC com um token SAS.  O modelo desenvolvido no tutorial de extensão do SQL é compartilhado em [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

3. Escolha **Abrir** para abrir o arquivo.

    Há quatro recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

1. Substitua a definição do parâmetro storageAccountKey pela definição de parâmetro a seguir:

    ```json
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the SAS token required to access the artifact location."
      }
    },
    ```

    ![Parâmetros de artefatos de seguro do tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

2. Atualize o valor dos três elementos a seguir do recurso de extensão do SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

O modelo concluído se parece com:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Implantar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implantação. Use o seguinte script de implantação do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).
Para os valores de _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, confira [Preparar um arquivo BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verificar a implantação

No portal, selecione o banco de dados SQL no grupo de recursos implantado recentemente. Selecione **Editor de consultas (versão prévia)** e insira as credenciais de administrador. Você deverá ver duas tabelas importadas para o banco de dados:

![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um SQL Server e um Banco de Dados SQL e importou um arquivo BACPAC usando o token SAS. Para saber como criar um Pipeline do Azure para desenvolver e implantar modelos do Gerenciador de Recursos

> [!div class="nextstepaction"]
> [Integração contínua com o Azure Pipeline](./resource-manager-tutorial-use-azure-pipelines.md)
