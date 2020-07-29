---
title: Usar CLI do Azure para atribuir uma função de RBAC para acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar CLI do Azure para atribuir permissões a uma entidade de segurança de Azure Active Directory com o RBAC (controle de acesso baseado em função). O armazenamento do Azure dá suporte a funções personalizadas e internas do Azure para autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: afa48c07e628eaeb013c24fa38fcda1adea2555a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368958"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Use CLI do Azure para atribuir uma função de RBAC para acesso a dados de BLOB e de fila

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar dados de BLOB ou de fila.

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar CLI do Azure para listar funções internas do Azure e atribuí-las aos usuários. Para obter mais informações sobre como usar CLI do Azure, consulte [interface de linha de comando (CLI) do Azure](/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Listar funções RBAC disponíveis

Para listar as funções internas do Azure disponíveis com CLI do Azure, use o comando [AZ role Definition List](/cli/azure/role/definition#az-role-definition-list) :

```azurecli-interactive
az role definition list --out table
```

Você verá as funções de dados de armazenamento do Azure internas listadas, junto com outras funções internas do Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Atribuir uma função de RBAC a uma entidade de segurança

Para atribuir uma função de RBAC a uma entidade de segurança, use o comando [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) . O formato do comando pode diferir com base no escopo da atribuição. Os exemplos a seguir mostram como atribuir uma função a um usuário em vários escopos, mas você pode usar o mesmo comando para atribuir uma função a qualquer entidade de segurança.

### <a name="container-scope"></a>Escopo do contêiner

Para atribuir uma função com escopo a um contêiner, especifique uma cadeia de caracteres que contenha o escopo do contêiner para o `--scope` parâmetro. O escopo de um contêiner está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** a um usuário, no escopo do nível do contêiner. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Escopo da fila

Para atribuir uma função com escopo a uma fila, especifique uma cadeia de caracteres que contenha o escopo da fila para o `--scope` parâmetro. O escopo de uma fila está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

O exemplo a seguir atribui a função de **colaborador de dados da fila de armazenamento** a um usuário, no escopo do nível da fila. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Escopo da conta de armazenamento

Para atribuir uma função com escopo à conta de armazenamento, especifique o escopo do recurso de conta de armazenamento para o `--scope` parâmetro. O escopo de uma conta de armazenamento está no formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O exemplo a seguir mostra como atribuir a função de **leitor de dados de blob de armazenamento** a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Escopo do grupo de recursos

Para atribuir uma função com escopo ao grupo de recursos, especifique o nome ou a ID do grupo de recursos para o `--resource-group` parâmetro. O exemplo a seguir atribui a função de **leitor de dados de fila de armazenamento** a um usuário no nível do grupo de recursos. Certifique-se de substituir os valores de exemplo e os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Escopo da assinatura

Para atribuir uma função com escopo à assinatura, especifique o escopo da assinatura para o `--scope` parâmetro. O escopo de uma assinatura está no formato:

```
/subscriptions/<subscription>
```

O exemplo a seguir mostra como atribuir a função de **leitor de dados de blob de armazenamento** a um usuário no nível da conta de armazenamento. Certifique-se de substituir os valores de exemplo pelos seus próprios valores: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Conceder acesso ao blob do Azure e a dados da fila com o RBAC usando Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Conceder acesso a dados de blob e fila do Azure com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
