---
title: Criar e gerenciar um instantâneo de blob no .NET
titleSuffix: Azure Storage
description: Saiba como criar um instantâneo somente leitura de um blob para fazer backup de dados de BLOB em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: be3d06d8ea493ab7c246ace5c49d5e5bc0036108
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463528"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Criar e gerenciar um instantâneo de blob no .NET

Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Este artigo mostra como criar e gerenciar instantâneos de BLOB usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Para obter mais informações sobre instantâneos de blob no armazenamento do Azure, consulte [criar e gerenciar um instantâneo de blob no .net](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Criar um instantâneo

# <a name="net-version-12x"></a>[Versão 12. x do .NET](#tab/v12)

Para criar um instantâneo de um blob de blocos usando a versão 12. x da biblioteca de cliente de armazenamento do Azure para .NET, use um dos seguintes métodos:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

O exemplo de código a seguir mostra como criar um instantâneo com a versão 12. x. Inclua uma referência à biblioteca [do Azure. Identity](https://www.nuget.org/packages/azure.identity) para usar suas credenciais do Azure ad para autorizar solicitações ao serviço.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-version-11x"></a>[Versão 11. x do .NET](#tab/v11)

Para criar um instantâneo de um blob de blocos usando a versão 11. x da biblioteca de cliente de armazenamento do Azure para .NET, use um dos seguintes métodos:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

O exemplo de código a seguir mostra como criar um instantâneo com a versão 11. x. Este exemplo especifica metadados adicionais para o instantâneo quando ele é criado.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Excluir instantâneos

Para excluir um blob, você deve primeiro excluir todos os instantâneos desse blob. Você pode excluir um instantâneo individualmente ou especificar que todos os instantâneos sejam excluídos quando o blob de origem for excluído. Se você tentar excluir um blob que ainda possua instantâneos, um erro será gerado.

# <a name="net-version-12x"></a>[Versão 12. x do .NET](#tab/v12)

Para excluir um blob e seus instantâneos usando a versão 12. x da biblioteca de cliente de armazenamento do Azure para .NET, use um dos seguintes métodos e inclua a enumeração [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) :

- [Excluir](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

O exemplo de código a seguir mostra como excluir um blob e seus instantâneos no .NET, em que `blobClient` é um objeto do tipo [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-version-11x"></a>[Versão 11. x do .NET](#tab/v11)

Para excluir um blob e seus instantâneos usando a versão 11. x da biblioteca de cliente de armazenamento do Azure para .NET, use um dos seguintes métodos de exclusão de BLOB e inclua a enumeração [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) :

- [Excluir](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

O exemplo de código a seguir mostra como excluir um blob e seus instantâneos no .NET, em que `blockBlob` é um objeto do tipo [CloudBlockBlob] [dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Próximas etapas

- [Instantâneos de BLOB](snapshots-overview.md)
- [Versões de BLOB (versão prévia)](versioning-overview.md)
- [Exclusão reversível para BLOBs](storage-blob-soft-delete.md)