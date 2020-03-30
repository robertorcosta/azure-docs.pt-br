---
title: Defina uma política de acesso armazenada com .NET - Azure Storage
description: Saiba como definir uma política de acesso armazenado usando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990735"
---
# <a name="define-a-stored-access-policy-with-net"></a>Defina uma política de acesso armazenada com .NET

Uma política de acesso armazenado fornece um nível adicional de controle sobre as assinaturas de acesso compartilhado (SAS) em nível de serviço no lado do servidor. A definição de uma política de acesso armazenado serve para agrupar assinaturas de acesso compartilhada e fornecer restrições adicionais para assinaturas de acesso compartilhada satisfatoidas pela política. Você pode usar uma política de acesso armazenada para alterar o tempo de início, o tempo de validade ou as permissões de um SAS ou revogá-la depois de emitido.
  
 Os seguintes recursos de armazenamento oferecem suporte às políticas de acesso armazenadas:  
  
- Contêineres de blob  
- Compartilhamentos de arquivos  
- Filas  
- Tabelas  
  
> [!NOTE]
> Uma política de acesso armazenado em um contêiner pode ser associada a uma assinatura de acesso compartilhada concedendo permissões ao próprio contêiner ou às bolhas que ele contém. Da mesma forma, uma política de acesso armazenado em um compartilhamento de arquivos pode ser associada a uma assinatura de acesso compartilhada concedendo permissões ao próprio compartilhamento ou aos arquivos que ele contém.  
>
> As políticas de acesso armazenadas são suportadas apenas para um SAS de serviço. As políticas de acesso armazenadas não são suportadas para sas de conta ou delegação de usuário SAS.  

## <a name="create-a-stored-access-policy"></a>Criar uma política de acesso armazenada

O código a seguir cria uma política de acesso armazenada em um contêiner. Você pode usar a política de acesso para especificar restrições para um serviço de SAS no contêiner ou seus blobs.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Confira também

- [Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](storage-sas-overview.md)
- [Definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy)

