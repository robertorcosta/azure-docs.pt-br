---
title: Crie um SAS de serviço para um contêiner ou blob com .NET
titleSuffix: Azure Storage
description: Saiba como criar um serviço de assinatura de acesso compartilhado (SAS) para um contêiner ou blob usando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 10045a760d7e0fcb02a754bc9bb52a5ebca970f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137204"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Crie um SAS de serviço para um contêiner ou blob com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar um SAS de serviço para um contêiner ou blob com a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-a-service-sas-for-a-blob-container"></a>Crie um SAS de serviço para um recipiente blob

Para criar um SAS de serviço para um contêiner, ligue para o método [CloudBlobContainer.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature)

O exemplo de código a seguir cria um SAS em um contêiner. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenado for fornecida, o código criará um SAS ad hoc no contêiner.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Crie um SAS de serviço para uma bolha

Para criar um SAS de serviço para uma bolha, chame o método [CloudBlob.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)

O exemplo de código a seguir cria um SAS em uma bolha. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenado for fornecida, o código criará um SAS ad hoc na bolha.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Próximas etapas

- [Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](../common/storage-sas-overview.md)
- [Criar uma SAS de serviço](/rest/api/storageservices/create-service-sas)
