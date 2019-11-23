---
title: Use the Azure portal to manage Azure AD access rights to blob and queue data with RBAC - Azure Storage | Microsoft Docs
description: Use role-based access control (RBAC) from the Azure portal to assign access to containers and queues to security principals. Azure Storage supports built-in and custom RBAC roles for authentication via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b11b2c42087b8724c7d90b87bc33965eb7270dc6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421985"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Grant access to Azure blob and queue data with RBAC in the Azure portal

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). Azure Storage defines a set of built-in RBAC roles that encompass common sets of permissions used to access blob or queue data. 

When an RBAC role is assigned to an Azure AD security principal, Azure grants access to those resources for that security principal. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. An Azure AD security principal may be a user, a group, an application service principal, or a [managed identity for Azure resources](../../active-directory/managed-identities-azure-resources/overview.md).

This article describes how to use the Azure portal to assign RBAC roles. The Azure portal provides a simple interface for assigning RBAC roles and managing access to your storage resources. You can also assign RBAC roles for blob and queue resources using Azure command-line tools or the Azure Storage management APIs. For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determine resource scope 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assign RBAC roles using the Azure portal

After you have determined the appropriate scope for a role assignment, navigate to that resource in the Azure portal. Display the **Access Control (IAM)** settings for the resource, and follow these instructions to manage role assignments:

1. Assign the appropriate Azure Storage RBAC role to grant access to an Azure AD security principal.

1. Assign the Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) role to users who need to access containers or queues via the Azure portal using their Azure AD credentials. 

The following sections describe each of these steps in more detail.

> [!NOTE]
> Como proprietário da conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados. Você deve atribuir explicitamente a si mesmo uma função RBAC para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível da sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.
> 
> You cannot assign a role scoped to a container or queue if your storage account has a hierarchical namespace enabled.

### <a name="assign-a-built-in-rbac-role"></a>Assign a built-in RBAC role

Before you assign a role to a security principal, be sure to consider the scope of the permissions you are granting. Review the [Determine resource scope](#determine-resource-scope) section to decide the appropriate scope.

O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila: 

1. In the [Azure portal](https://portal.azure.com), go to your storage account and display the **Overview** for the account.
1. Em Serviços, selecione **Blobs**. 
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner. 
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Screenshot showing container access control settings](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. In the **Add role assignment** window, select the Azure Storage role that you want to assign. Then search to locate the security principal to which you want to assign that role.

    ![Captura de tela que mostra como atribuir uma função do RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Clique em **Save** (Salvar). A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    ![Screenshot showing list of users assigned to a role](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

You can follow similar steps to assign a role scoped to the storage account, resource group, or subscription.

### <a name="assign-the-reader-role-for-portal-access"></a>Assign the Reader role for portal access

When you assign a built-in or custom role for Azure Storage to a security principal, you are granting permissions to that security principal to perform operations on data in your storage account. The built-in **Data Reader** roles provide read permissions for the data in a container or queue, while the built-in **Data Contributor** roles provide read, write, and delete permissions to a container or queue. Permissions are scoped to the specified resource.  

For example, if you assign the **Storage Blob Data Contributor** role to user Mary at the level of a container named **sample-container**, then Mary is granted read, write, and delete access to all of the blobs in that container.

However, if Mary wants to view a blob in the Azure portal, then the **Storage Blob Data Contributor** role by itself will not provide sufficient permissions to navigate through the portal to the blob in order to view it. Additional Azure AD permissions are required to navigate through the portal and view the other resources that are visible there.

If your users need to be able to access blobs in the Azure portal, then assign them an additional RBAC role, the [Reader](../../role-based-access-control/built-in-roles.md#reader) role, to those users, at the level of the storage account or above. The **Reader** role is an Azure Resource Manager role that permits users to view storage account resources, but not modify them. It does not provide read permissions to data in Azure Storage, but only to account management resources.

Follow these steps to assign the **Reader** role so that a user can access blobs from the Azure portal. In this example, the assignment is scoped to the storage account:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Select **Access control (IAM)** to display the access control settings for the storage account. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. In the **Add role assignment** window, select the **Reader** role. 
1. From the **Assign access to** field, select **Azure AD user, group, or service principal**.
1. Search to locate the security principal to which you want to assign the role.
1. Save the role assignment.

> [!NOTE]
> Assigning the Reader role is necessary only for users who need to access blobs or queues using the Azure portal. 

## <a name="next-steps"></a>Próximos passos

- For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md). 
- Para saber mais sobre o RBAC, consulte [O que é o Controle de Acesso Baseado em Função (RBAC)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
