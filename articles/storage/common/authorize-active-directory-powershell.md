---
title: Execute comandos PowerShell com credenciais Ad do Azure para acessar dados de blob ou fila
titleSuffix: Azure Storage
description: O PowerShell suporta a assinatura com credenciais Ad do Azure para executar comandos no Blob de Armazenamento Do Azure e filas de dados. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída ao diretor de segurança do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553443"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Execute comandos PowerShell com credenciais Ad do Azure para acessar dados de blob ou fila

O Azure Storage fornece extensões para o PowerShell que permitem que você faça login e execute comandos de scripting com credenciais do Azure Active Directory (Azure AD). Quando você faz login no PowerShell com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Esse token é usado automaticamente pela PowerShell para autorizar operações de dados subseqüentes contra o armazenamento Blob ou Queue. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões para blob e dados de fila para um principal de segurança Azure AD através do RBAC (Role-Based Access Control). Para obter mais informações sobre as funções RBAC no Azure Storage, consulte [Gerenciar os direitos de acesso aos dados de armazenamento do Azure com o RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações com suporte

As extensões de armazenamento do Azure são suportadas para operações em dados de blob e fila. Quais operações você pode chamar depende das permissões concedidas ao diretor de segurança do Azure AD com o qual você faz login no PowerShell. Permissões para contêineres ou filas de armazenamento do Azure são atribuídas via RBAC. Por exemplo, se você tiver sido designado para a função **Blob Data Reader,** então você pode executar comandos de scripting que lêem dados de um contêiner ou fila. Se você tiver sido designado para a função **Blob Data Contributor,** então você pode executar comandos de scripting que lêem, escrevem ou excluem um contêiner ou fila ou os dados que eles contêm.

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure em um contêiner ou fila, consulte [Operações de armazenamento de chamadas com tokens OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chamar comandos PowerShell usando credenciais Ad do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para usar o Azure PowerShell para fazer login e executar operações subseqüentes contra o Azure Storage `-UseConnectedAccount` usando credenciais Azure AD, crie um contexto de armazenamento para referenciar a conta de armazenamento e inclua o parâmetro.

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento do Azure PowerShell usando suas credenciais Azure AD. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

1. Faça login na sua conta do Azure com o comando [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Para obter mais informações sobre como entrar no Azure com o PowerShell, consulte [Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Crie um grupo de recursos do Azure chamando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Crie uma conta de armazenamento chamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obtenha o contexto da conta de armazenamento que especifica a nova conta de armazenamento chamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de passar repetidamente as credenciais. Inclua `-UseConnectedAccount` o parâmetro para chamar quaisquer operações de dados subseqüentes usando suas credenciais Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o contêiner, atribua a função [de contribuinte de dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de armazenamento a si mesmo. Mesmo que você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados contra a conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [O acesso de Grant ao Blob do Azure e os dados de fila com o RBAC no portal Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função RBAC podem levar alguns minutos para se propagar.

1. Crie um contêiner chamando [new-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Como esta chamada usa o contexto criado nas etapas anteriores, o contêiner é criado usando suas credenciais azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Próximas etapas

- [Use o PowerShell para atribuir uma função RBAC para acesso a dados de blob e fila](storage-auth-aad-rbac-powershell.md)
- [Autorize o acesso a dados de blob e fila com identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
