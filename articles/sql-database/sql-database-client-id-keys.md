---
title: Get values for app authentication
description: Crie uma entidade de serviço para acessar o Banco de Dados SQL do código.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d357740d340b248859d6dfadf73f83b6e6bb8014
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421313"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtenha os valores necessários para autenticar um aplicativo para acessar o Banco de Dados SQL do código

Para criar e gerenciar o Banco de Dados SQL do código, você deve registrar seu aplicativo no domínio do AAD (Azure Active Directory) na assinatura em que os recursos do Azure foram criados.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Criar uma entidade de serviço para acessar os recursos de um aplicativo

The following examples create the Active Directory (AD) application and the service principal that we need to authenticate our C# app. O script gera os valores necessários para o exemplo anterior do C#. Para obter informações detalhadas, consulte [usar o Azure PowerShell para criar uma entidade de serviço para acessar os recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
# sign in to Azure
Connect-AzAccount

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#Set-AzContext -SubscriptionId $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

# create a Service Principal for the app
$svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause here for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azure-cli
# sign in to Azure
az login

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#az account set --subscription $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = az ad app create --display-name $appName --homepage $Uri --identifier-uris $Uri --password $secret

# create a Service Principal for the app
$svcprincipal = az ad sp create --id $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = az role assignment create --role "Contributor" --assignee $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (az account show --query "id")
Write-Output "_tenantId:" (az account show --query "tenantId")
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

* * *

## <a name="see-also"></a>Consulte

[Criar um Banco de Dados SQL com C#](sql-database-get-started-csharp.md)  
[Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)
