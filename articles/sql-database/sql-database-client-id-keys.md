---
title: Obter valores para autenticação de aplicativo
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
ms.openlocfilehash: 1c2f45aeeaadbbaedc839dd0e2c10804d720a6cd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827173"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtenha os valores necessários para autenticar um aplicativo para acessar o Banco de Dados SQL do código

Para criar e gerenciar o Banco de Dados SQL do código, você deve registrar seu aplicativo no domínio do AAD (Azure Active Directory) na assinatura em que os recursos do Azure foram criados.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Criar uma entidade de serviço para acessar os recursos de um aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e a entidade de serviço necessária para autenticar nosso aplicativo C#. O script gera os valores necessários para o exemplo anterior do C#. Para obter informações detalhadas, consulte [usar o Azure PowerShell para criar uma entidade de serviço para acessar os recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create an AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Confira também
* [Criar um Banco de Dados SQL com C#](sql-database-get-started-csharp.md)
* [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)

