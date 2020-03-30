---
title: Listar bolhas com .NET - Armazenamento Azure
description: Saiba como listar blobs em um contêiner em sua conta do Azure Storage usando a biblioteca de clientes .NET. Exemplos de código mostram como listar blobs em uma listagem plana ou como listar blobs hierarquicamente, como se estivessem organizados em diretórios ou pastas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137913"
---
# <a name="list-blobs-with-net"></a>Liste blobs com .NET

Quando você lista blobs do seu código, você pode especificar uma série de opções para gerenciar como os resultados são retornados do Azure Storage. Você pode especificar o número de resultados a retornar em cada conjunto de resultados e, em seguida, recuperar os conjuntos subseqüentes. Você pode especificar um prefixo para retornar blobs cujos nomes começam com esse caractere ou string. E você pode listar bolhas em uma estrutura de listagem plana, ou hierarquicamente. Uma listagem hierárquica retorna blobs como se estivessem organizados em pastas. 

Este artigo mostra como listar blobs usando a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Entenda as opções de listagem de blob

Para listar os blobs em uma conta de armazenamento, ligue para um desses métodos:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Para listar as bolhas em um recipiente, ligue para um desses métodos:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

As sobrecargas desses métodos fornecem opções adicionais para gerenciar como as bolhas são devolvidas pela operação de listagem. Essas opções são descritas nas seções a seguir.

### <a name="manage-how-many-results-are-returned"></a>Gerencie quantos resultados são devolvidos

Por padrão, uma operação de listagem retorna até 5000 resultados por vez. Para retornar um conjunto menor de resultados, forneça `maxresults` um valor não zero para o parâmetro ao chamar um dos **métodos ListBlobs.**

Se uma operação de listagem retornar mais de 5000 blobs, ou se você tiver especificado um valor para `maxresults` tal forma que a operação de listagem devolva um subconjunto de contêineres na conta de armazenamento, então o Azure Storage retorna um token de *continuação* com a lista de blobs. Um token de continuação é um valor opaco que você pode usar para recuperar o próximo conjunto de resultados do Azure Storage.

Em seu código, verifique o valor do token de continuação para determinar se ele é nulo. Quando o token de continuação é nulo, então o conjunto de resultados está completo. Se o token de continuação não for nulo, então ligue novamente a operação de listagem de chamadas, passando o token de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar resultados com um prefixo

Para filtrar a lista de recipientes, especifique uma string para o `prefix` parâmetro. A seqüência de prefixo pode incluir um ou mais caracteres. O Azure Storage então retorna apenas os blobs cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Metadados de retorno

Para retornar metadados blob com os resultados, especifique o valor **de Metadados** para a enumeração [BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) O Azure Storage inclui metadados com cada bolha devolvida, então você não precisa chamar um dos **métodos FetchAttributes** neste contexto para recuperar os metadados blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Listagem plana versus listagem hierárquica

Blobs no Azure Storage são organizados em um paradigma plano, em vez de um paradigma hierárquico (como um sistema de arquivos clássico). No entanto, você pode organizar bolhas em *diretórios virtuais,* a fim de imitar uma estrutura de pasta. Um diretório virtual faz parte do nome da bolha e é indicado pelo caractere delimitador.

Para organizar bolhas em diretórios virtuais, use um caractere delimitador no nome blob. O caractere delimitador padrão é uma barra para a frente (/), mas você pode especificar qualquer caractere como delimitador.

Se você nomear suas bolhas usando um delimitador, então você pode optar por listar bolhas hierarquicamente. Para uma operação hierárquica de listagem, o Azure Storage retorna quaisquer diretórios virtuais e bolhas abaixo do objeto pai. Você pode chamar a operação de listagem recursivamente para atravessar a hierarquia, semelhante à forma como você atravessaria um sistema de arquivos clássico programáticamente.

## <a name="use-a-flat-listing"></a>Use uma listagem plana

Por padrão, uma operação de listagem retorna blobs em uma listagem plana. Em uma listagem plana, blobs não são organizados por diretório virtual.

O exemplo a seguir lista as bolhas no recipiente especificado usando uma listagem plana, com um tamanho de segmento opcional especificado e grava o nome blob em uma janela do console.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A saída da amostra é semelhante a:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Use uma listagem hierárquica

Quando você chama uma operação de listagem hierarquicamente, o Azure Storage retorna os diretórios virtuais e blobs no primeiro nível da hierarquia. A propriedade [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) de cada diretório virtual é definida para que você possa passar o prefixo em uma chamada recursiva para recuperar o próximo diretório.

Para listar bolhas hierarquicamente, defina o `useFlatBlobListing` parâmetro do método de listagem como **falso**.

O exemplo a seguir lista as bolhas no recipiente especificado usando uma listagem plana, com um tamanho de segmento opcional especificado e grava o nome blob na janela do console.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A saída da amostra é semelhante a:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Os instantâneos blob não podem ser listados em uma operação hierárquica de listagem.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Próximas etapas

- [Blobs de lista](/rest/api/storageservices/list-blobs)
- [Enumerando recursos de blob](/rest/api/storageservices/enumerating-blob-resources)
