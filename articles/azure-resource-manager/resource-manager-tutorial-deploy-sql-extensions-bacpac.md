---
title: Importar arquivos BACPAC do SQL com modelos
description: Saiba como usar a extensão de Banco de Dados SQL para importar arquivos BACPAC do SQL com modelos do Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9f5e2e402e13076dc538a9c9d55e5b67e0d86d4f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978878"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager

Saiba como usar a extensão de Banco de Dados SQL do Azure para importar um arquivo BACPAC com modelos do Azure Resource Manager. Artefatos de implantação são quaisquer arquivos, além dos arquivos de modelo principais, necessários para concluir uma implantação. O arquivo BACPAC é um artefato. Neste tutorial, você criará um modelo para implantar um Azure SQL Server e um Banco de Dados SQL e importar um arquivo BACPAC. Para obter informações sobre como implantar extensões de máquina virtual do Azure usando modelos do Azure Resource Manager, confira o [# Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um arquivo BACPAC
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador do SQL Server. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um arquivo BACPAC

Um arquivo BACPAC é compartilhado no [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Para criar seus próprio arquivo, confira [Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC](../sql-database/sql-database-export.md). Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o modelo posteriormente no tutorial.

O arquivo BACPAC deve ser armazenado em uma conta de armazenamento do Azure antes que possa ser importado usando o modelo do Resource Manager. O script do PowerShell a seguir prepara o arquivo BACPAC com estas etapas:

* Baixe o arquivo BACPAC.
* Criar uma conta do Armazenamento do Azure.
* Crie um contêiner de blobs da conta de armazenamento.
* Carregue o arquivo BACPAC para o contêiner.
* Exiba a chave da conta de armazenamento e a URL do blob.

1. Selecione **Experimentar** para abrir o Cloud Shell e, em seguida, cole o script do PowerShell a seguir na janela do shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Anote a chave da conta de armazenamento e a URL do arquivo BACPAC. Você precisará desses valores ao implantar o modelo.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

O modelo usado neste tutorial é armazenado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.

    Há dois recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

1. Adicione mais dois parâmetros ao final da seção de **parâmetros** para definir a chave de conta de armazenamento e a URL do BACPAC:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Adicione uma vírgula após **adminPassword**. Para formatar o arquivo JSON do VS Code, pressione **[SHIFT]+[ALT]+F**.

    Confira [Preparar um arquivo BACPAC](#prepare-a-bacpac-file) sobre como obter esses dois valores.

1. Adicione dois recursos extras ao modelo.

    * Para permitir que a extensão do Banco de Dados SQL importe arquivos BACPAC, você precisará permitir tráfego dos serviços do Azure. Adicione a seguinte definição de regra de firewall à definição do SQL Server:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        O modelo ficará mais ou menos assim:

        ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Adicione um recurso de extensão do Banco de Dados SQL à definição de banco de dados com o seguinte JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        O modelo ficará mais ou menos assim:

        ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Para entender a definição de recurso, confira a [referência de extensão de Banco de Dados SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Abaixo estão alguns elementos importantes:

        * **dependsOn**: o recurso de extensão deve ser criado depois que o Banco de Dados SQL foi criado.
        * **storageKeyType**: Especifique o tipo da chave de armazenamento a ser usada. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Use `StorageAccessKey` neste tutorial.
        * **storageKey**: Especifique a chave para a conta de armazenamento em que o arquivo BACPAC está armazenado. Se o tipo de chave de armazenamento é SharedAccessKey, ele deve ser precedido por um "?"
        * **storageUri**: Especifique a URL do arquivo BACPAC armazenado em uma conta de armazenamento.
        * **administratorLoginPassword**: a senha do administrador do SQL. Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).

O modelo concluído se parece com:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Implantar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implantação. Use o seguinte script de implantação do PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Considere usar o mesmo nome de projeto usado quando você preparou o arquivo bacpac, para que todos os recursos sejam armazenados no mesmo grupo de recursos.  É mais fácil para gerenciar recursos, como limpar os recursos. Se você usar o mesmo nome de projeto, poderá remover o comando **New-AzResourceGroup** do script ou responder y ou n quando for perguntado se deseja atualizar o grupo de recursos existente.

Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implantação

Para acessar o SQL Server do computador cliente, você precisa adicionar uma regra de firewall extra. Para obter mais informações, confira [Criar e gerenciar regras de firewall de IP](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

No portal, selecione o banco de dados SQL no grupo de recursos implantado recentemente. Selecione **Editor de consultas (versão prévia)** e insira as credenciais de administrador. Você deverá ver duas tabelas importadas para o banco de dados:

![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um SQL Server e um Banco de Dados SQL e importou um arquivo BACPAC. O arquivo BACPAC é armazenado em uma conta de Armazenamento do Azure. Qualquer pessoa com a URL pode acessar o arquivo. Para saber como proteger o arquivo BACPAC (artefato), confira

> [!div class="nextstepaction"]
> [Proteger os artefatos](./resource-manager-tutorial-secure-artifacts.md)
