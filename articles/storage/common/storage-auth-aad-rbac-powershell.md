---
title: Use o PowerShell para atribuir uma função RBAC para acesso a dados
titleSuffix: Azure Storage
description: Aprenda a usar o PowerShell para atribuir permissões a um diretor de segurança do Azure Active Directory com rbac (Role-based Access Control, controle de acesso baseado em função). O Azure Storage suporta funções RBAC incorporadas e personalizadas para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460574"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Use o PowerShell para atribuir uma função RBAC para acesso a dados de blob e fila

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Um diretor de segurança do Azure AD pode ser um usuário, um grupo, um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o Azure PowerShell para listar funções RBAC incorporadas e atribuí-las aos usuários. Para obter mais informações sobre como usar o Azure PowerShell, consulte [Visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo dos recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Listar as funções de RBAC disponíveis

Para listar as funções de RBAC incorporadas disponíveis com o Azure PowerShell, use o comando [Get-AzRoleDefinition:](/powershell/module/az.resources/get-azroledefinition)

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Você verá as funções de dados de armazenamento azure incorporadas listadas, juntamente com outras funções incorporadas para o Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Atribuir uma função RBAC a um diretor de segurança

Para atribuir uma função RBAC a um diretor de segurança, use o comando [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) O formato do comando pode diferir com base no escopo da atribuição. Para executar o comando, você precisa ter a função Proprietário ou Contribuinte atribuída no escopo correspondente. Os exemplos a seguir mostram como atribuir uma função a um usuário em vários escopos, mas você pode usar o mesmo comando para atribuir uma função a qualquer principal de segurança.

### <a name="container-scope"></a>Escopo do contêiner

Para atribuir uma função escopo a um recipiente, especifique uma `--scope` seqüência contendo o escopo do recipiente para o parâmetro. O escopo de um contêiner está na forma:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

O exemplo a seguir atribui a função **De contribuinte de dados blob de armazenamento** a um usuário, escopo para um contêiner chamado *sample-container*. Certifique-se de substituir os valores de amostra e os valores do espaço reservado entre parênteses com seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Escopo de fila

Para atribuir uma função escopo a uma fila, especifique uma string `--scope` contendo o escopo da fila para o parâmetro. O escopo de uma fila está no formulário:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

O exemplo a seguir atribui a função **Contribuinte de dados da fila de armazenamento** a um usuário, escopo para uma fila chamada fila de *amostras*. Certifique-se de substituir os valores de amostra e os valores do espaço reservado entre parênteses com seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Escopo da conta de armazenamento

Para atribuir uma função escopo à conta de armazenamento, especifique o escopo do recurso da conta de armazenamento para o `--scope` parâmetro. O escopo de uma conta de armazenamento está no formulário:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo a seguir mostra como escopo da função **'Leitor de dados blob** de armazenamento' para um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de amostra por seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Escopo do grupo de recursos

Para atribuir uma função escopo ao grupo de recursos, especifique o nome do grupo de recursos ou ID para o `--resource-group` parâmetro. O exemplo a seguir atribui a função **Leitor de dados da fila de armazenamento** a um usuário no nível do grupo de recursos. Certifique-se de substituir os valores de amostra e os valores de espaço reservado entre parênteses com seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Escopo de assinatura

Para atribuir uma função escopo à assinatura, especifique `--scope` o escopo da assinatura para o parâmetro. O escopo de uma assinatura está no formulário:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a função **de leitor de dados blob de armazenamento** a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de amostra por seus próprios valores: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
