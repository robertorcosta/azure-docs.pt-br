---
title: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como usar a extensão de Banco de Dados SQL para importar arquivos BACPAC do SQL com modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 239bb77d486e8cb845ec439d84def5e34cf64348
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170223"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager

Saiba como usar a extensão de Banco de Dados SQL do Azure para importar um arquivo BACPAC com modelos do Azure Resource Manager. Artefatos de implantação são quaisquer arquivos, além do arquivo de modelo principal, necessários para concluir uma implantação. O arquivo BACPAC é um artefato. Neste tutorial, você criará um modelo para implantar um Azure SQL Server e um Banco de Dados SQL e importar um arquivo BACPAC. Para obter informações sobre como implantar extensões de máquina virtual do Azure usando modelos do Azure Resource Manager, confira o [# Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager. Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador do SQL Server. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um arquivo BACPAC

Um arquivo BACPAC é compartilhado no [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Para criar seus próprio arquivo, confira [Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC](../sql-database/sql-database-export.md). Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

O modelo usado neste tutorial é armazenado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.

    Há três recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione dois recursos extras ao modelo.

* Para permitir que a extensão do banco de dados SQL importe arquivos BACPAC, você precisará permitir o acesso a serviços do Azure. Adicione o seguinte JSON à definição do SQL Server:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    O modelo ficará mais ou menos assim:

    ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* Adicione um recurso de extensão do Banco de Dados SQL à definição de banco de dados com o seguinte JSON:

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    O modelo ficará mais ou menos assim:

    ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Para entender a definição de recurso, confira a [referência de extensão de Banco de Dados SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Abaixo estão alguns elementos importantes:

    * **dependsOn**: o recurso de extensão deve ser criado depois que o Banco de Dados SQL foi criado.
    * **storageKeyType**: o tipo da chave de armazenamento a ser usada. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Como o arquivo BACPAC fornecido é compartilhado em uma conta do Armazenamento do Azure com acesso público, 'SharedAccessKey' é usado aqui.
    * **storageKey**: a chave de armazenamento a ser usada. Se o tipo de chave de armazenamento é SharedAccessKey, ele deve ser precedido por um "?."
    * **storageUri**: o URI de armazenamento a ser usado. Se você optar por não usar o arquivo BACPAC fornecido, precisará atualizar os valores.
    * **administratorLoginPassword**: a senha do administrador do SQL. Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).

## <a name="deploy-the-template"></a>Implantar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implantação. Use o seguinte script de implantação do PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Use a senha gerada. Consulte [Pré-requisitos](#prerequisites).

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

Neste tutorial, você implantou um SQL Server e um Banco de Dados SQL e importou um arquivo BACPAC. O arquivo BACPAC é armazenado em uma conta de Armazenamento do Azure. Qualquer pessoa com a URL pode acessar o arquivo. Para saber como proteger o arquivo BACPAC (artefato), confira

> [!div class="nextstepaction"]
> [Proteger os artefatos](./resource-manager-tutorial-secure-artifacts.md)
