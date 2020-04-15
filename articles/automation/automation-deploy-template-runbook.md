---
title: Implantar um modelo do Azure Resource Manager em um runbook de Automação do Azure
description: Como implantar um modelo do Azure Resource Manager armazenado no Armazenamento do Azure de um runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell,  runbook, json, automação do azure
ms.openlocfilehash: 575f954b346edb7d682e3fd0b432a257486bbfbb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383289"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Implantar um modelo do Azure Resource Manager em um runbook do PowerShell de Automação do Azure

Você pode escrever um [runbook do PowerShell de Automação do Azure](automation-first-runbook-textual-powershell.md) que implanta um recurso do Azure usando um [modelo do Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Ao fazer isso, você pode automatizar a implantação de recursos do Azure. Você pode manter seus modelos do Gerenciador de Recursos em um local central seguro, como no Armazenamento do Azure.

Neste artigo, criamos um runbook do PowerShell que usa um modelo do Gerenciador de Recursos armazenado no [Armazenamento do Azure](../storage/common/storage-introduction.md) para implantar uma nova conta de Armazenamento do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes itens:

* Assinatura do Azure. Se você ainda não tiver um, você pode [ativar seus benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou se inscrever em uma conta [gratuita](https://azure.microsoft.com/free/).
* [Conta de automação](automation-sec-configure-azure-runas-account.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* [Conta de Armazenamento do Azure](../storage/common/storage-create-storage-account.md) na qual será armazenado o modelo do Resource Manager
* Azure PowerShell instalado em uma máquina local. Consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

Para este exemplo, usamos um modelo do Gerenciador de Recursos que implanta uma nova conta de Armazenamento do Azure.

Em um editor de texto, copie o seguinte texto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Salvar o arquivo localmente como **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salve o modelo do Resource Manager no Armazenamento do Azure

Agora usamos o PowerShell para criar um compartilhamento de arquivos do Azure Storage e fazer upload do arquivo **TemplateTest.json.**
Para obter instruções sobre como criar um compartilhamento de arquivos e carregar um arquivo no portal do Azure, consulte [Introdução ao armazenamento de arquivos do Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Inicie o PowerShell no computador local e execute os seguintes comandos para criar um compartilhamento de arquivos e carregar o modelo do Resource Manager para o compartilhamento de arquivos.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Criar o script de runbook do PowerShell

Agora criamos um script PowerShell que recebe o arquivo **TemplateTest.json** do Azure Storage e implanta o modelo para criar uma nova conta do Azure Storage.

Em um editor de texto, cole o seguinte texto:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Salvar o arquivo localmente como **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importar e publicar o runbook em sua conta de Automação do Azure

Agora podemos usar o PowerShell para importar o runbook para sua conta de Automação do Azure e, em seguida, publicar o runbook.
Para obter informações sobre como importar e publicar um runbook no portal do Azure, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).

Para importar **DeployTemplate.ps1** em sua conta de automação como um runbook do PowerShell, execute os seguintes comandos do PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Iniciar o runbook

Agora começamos o runbook chamando o [cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) Para obter informações sobre como iniciar um runbook no portal do Azure, consulte [Iniciar um runbook na Automação do Azure](automation-starting-a-runbook.md).

Execute os seguintes comandos no console do PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

O runbook é executado e você pode verificar o status executando `$job.Status`.

O runbook obtém o modelo do Resource Manager e o utiliza para implantar uma nova conta de Armazenamento do Azure.
Você pode ver que a nova conta de armazenamento foi criada executando o seguinte comando:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Resumo

É isso! Agora você pode usar o Azure Automation e o Azure Storage com modelos de Gerenciador de Recursos para implantar todos os seus recursos do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os modelos do Gerenciador de recursos, consulte [a visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Para começar a usar o Armazenamento do Azure, consulte [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md).
* Para localizar outros runbooks úteis de Automação do Azure, consulte [Galerias de runbooks e módulos para Automação do Azure](automation-runbook-gallery.md).
* Para encontrar outros modelos úteis do Gerenciador de recursos, consulte [Modelos de inicialinicialidade do Azure Quickstart](https://azure.microsoft.com/resources/templates/).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
