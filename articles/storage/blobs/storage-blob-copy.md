---
title: Copiar uma bolha com .NET - Armazenamento Azure
description: Aprenda a copiar uma bolha em sua conta do Azure Storage usando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135880"
---
# <a name="copy-a-blob-with-net"></a>Copiar uma bolha com .NET

Este artigo demonstra como copiar uma bolha com uma conta do Azure Storage. Também mostra como abortar uma operação de cópia assíncrona. O código de exemplo usa a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Sobre copiar blobs

Quando você copia uma bolha dentro da mesma conta de armazenamento, é uma operação síncrona. Quando você copia através de contas é uma operação assíncrona. Os métodos [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) e [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) retornam um valor de ID de cópia usado para verificar o status ou abortar a operação de cópia.

A bolha de origem para uma operação de cópia pode ser uma bolha de bloco, uma bolha de apêndice, uma bolha de página ou um instantâneo. Se o blob de destino já existir, ele deverá ser do mesmo tipo do blob de origem. Qualquer blob de destino existente será substituído. 

A bolha de destino não pode ser modificada enquanto uma operação de cópia estiver em andamento. Um blob de destino pode ter apenas uma operação pendente de blob de cópia. Em outras palavras, uma bolha não pode ser o destino de várias operações de cópia pendentes.

Toda a bolha de origem ou arquivo é sempre copiado. Não é suportado copiar uma série de bytes ou conjunto de blocos.

Quando uma bolha é copiada, suas propriedades do sistema são copiadas para a bolha de destino com os mesmos valores.

Para todos os tipos de blob, você pode verificar a propriedade [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) na bolha de destino para obter o status da operação de cópia. O blob final será confirmado quando a cópia for concluída.

Uma operação de cópia pode pegar qualquer um dos seguintes formulários:

  - É possível copiar um blob de origem em um blob de destino com outro nome. A bolha de destino pode ser uma bolha existente do mesmo tipo blob (bloco, apêndice ou página), ou pode ser uma nova bolha criada pela operação de cópia.
  - Você pode copiar uma bolha de origem para uma bolha de destino com o mesmo nome, substituindo efetivamente a bolha de destino. Essa operação de cópia remove quaisquer blocos não comprometidos e substitui os metadados do bolha de destino.
  - Você pode copiar um arquivo de origem no serviço Arquivo Azure para uma bolha de destino. A bolha de destino pode ser uma bolha de bloco existente, ou pode ser uma nova bolha de bloco criada pela operação de cópia. A cópia de arquivos para blobs de página ou bolhas de apêndice não é suportada.
  - Você pode copiar um instantâneo sobre seu blob de base. Promovendo um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob.
  - Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.

## <a name="copy-a-blob"></a>Copiar uma bolha

Para copiar uma bolha, ligue para um dos seguintes métodos:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

O exemplo de código a seguir recebe uma referência a uma bolha criada anteriormente e copia-a para uma nova bolha no mesmo recipiente:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Abortar uma operação de cópia de bolha

Abortar uma operação de cópia resulta em uma bolha de destino de comprimento zero para bolhas de bloco, bolhas de apêndice e bolhas de página. No entanto, os metadados para a bolha de destino terão os novos valores copiados da bolha de origem ou definidos explicitamente na chamada [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ou [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Para manter os metadados originais antes da cópia, faça `StartCopy` um `StartCopyAsync`instantâneo da bolha de destino antes de ligar ou .

Quando você aborta uma operação de cópia de blob em andamento, o [copyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) da bolha de destino é definido como [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Os métodos [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) cancelam uma operação contínua de cópia de bolhas e deixam uma bolha de destino com zero comprimento e metadados completos.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Próximas etapas

Os tópicos a seguir contêm informações sobre copiar blobs e abortar operações de cópia em andamento usando as APIs Azure REST.

- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Anular copiar Blob](/rest/api/storageservices/abort-copy-blob)
