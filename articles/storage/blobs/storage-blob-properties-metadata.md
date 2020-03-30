---
title: Gerenciar propriedades e metadados para uma bolha com .NET - Armazenamento Azure
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em blobs em sua conta do Azure Storage usando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137655"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gerenciar propriedades e metadados blob com .NET

Além dos dados que eles contêm, os blobs suportam propriedades do sistema e metadados definidos pelo usuário. Este artigo mostra como gerenciar propriedades do sistema e metadados definidos pelo usuário com a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: Existem propriedades do sistema em cada recurso de armazenamento Blob. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca cliente do Azure Storage para .NET mantém essas propriedades para você.

- **Metadados definidos pelo usuário**: Os metadados definidos pelo usuário consistem em um ou mais pares de valor de nome que você especifica para um recurso de armazenamento Blob. Você pode usar metadados para armazenar valores adicionais com o recurso. Os valores de metadados são apenas para seus próprios propósitos e não afetam como o recurso se comporta.

Recuperar metadados e valores de propriedade para um recurso de armazenamento Blob é um processo de duas etapas. Antes de ler esses valores, você deve `FetchAttributes` buscá-los explicitamente chamando o método ou. `FetchAttributesAsync` A exceção a esta `Exists` `ExistsAsync` regra é que `FetchAttributes` os métodos e os métodos chamam o método apropriado as capas. Quando você chama um desses métodos, você não `FetchAttributes`precisa também chamar .

> [!IMPORTANT]
> Se você encontrar que os valores de propriedade ou metadados para um `FetchAttributes` `FetchAttributesAsync` recurso de armazenamento não foram preenchidos, verifique se o seu código chama o método ou.

## <a name="set-and-retrieve-properties"></a>Definir e recuperar propriedades

O exemplo de `ContentType` código `ContentLanguage` a seguir define as propriedades do sistema em uma bolha.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Para recuperar propriedades blob, ligue `FetchAttributes` para o método ou `FetchAttributesAsync` em sua bolha para preencher a `Properties` propriedade. O exemplo de código a seguir obtém as propriedades do sistema de uma bolha e exibe alguns dos valores:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Definir e recuperar metadados

Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir metadados, adicione pares de `Metadata` valor de nome à coleção no recurso. Em seguida, chame um dos seguintes métodos para escrever os valores:

- [Setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Os pares de nomes/valor de metadados são cabeçalhos HTTP válidos e devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho HTTP válidos e identificadores C# válidos, podem conter apenas caracteres ASCII e devem ser tratados como insensíveis a casos. [Valores de metadados de codificação base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) ou [URL-encode](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) contendo caracteres não-ASCII.

O nome dos metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores de C#. Os nomes de metadados mantêm o caso usado quando foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados usando o mesmo nome forem enviados para um recurso, o armazenamento Azure Blob reameite o código de erro HTTP 400 (Solicitação Ruim).

O exemplo de código a seguir define metadados em uma bolha. Um valor é definido usando `Add` o método da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Para recuperar metadados, `FetchAttributes` `FetchAttributesAsync` ligue para o método ou `Metadata` ou em sua bolha ou contêiner para preencher a coleção e leia os valores, como mostrado no exemplo abaixo.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Confira também

- [Operação Propriedades de Conjunto Blob](/rest/api/storageservices/set-blob-properties)
- [Obter operação Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Definir operação Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Obter operação Blob Metadata](/rest/api/storageservices/get-blob-metadata)
