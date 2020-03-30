---
title: Use .NET para gerenciar propriedades e metadados para um recipiente blob
titleSuffix: Azure Storage
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em recipientes blob em sua conta do Azure Storage usando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135914"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gerenciar propriedades de contêiner e metadados com .NET

Os contêineres Blob suportam propriedades do sistema e metadados definidos pelo usuário, além dos dados que contêm. Este artigo mostra como gerenciar propriedades do sistema e metadados definidos pelo usuário com a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: Existem propriedades do sistema em cada recurso de armazenamento Blob. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca cliente do Azure Storage para .NET mantém essas propriedades para você.

- **Metadados definidos pelo usuário**: Os metadados definidos pelo usuário consistem em um ou mais pares de valor de nome que você especifica para um recurso de armazenamento Blob. Você pode usar metadados para armazenar valores adicionais com o recurso. Os valores de metadados são apenas para suas próprias finalidades e não afetam o comportamento do recurso.

Recuperar valores de propriedade e metadados para um recurso de armazenamento Blob é um processo de duas etapas. Antes de poder ler esses valores, será necessário buscá-los explicitamente, chamando o método **FetchAttributes** ou **FetchAttributesAsync**. A exceção a esta regra é que os métodos **Existe** e **ExisteAsync** chamam o método FetchAttributes apropriado as **capas.** Quando você chama um desses métodos, você não precisa também chamar **FetchAttributes**.

> [!IMPORTANT]
> Se você achar que a propriedade ou os valores de metadados de um recurso de armazenamento não foram preenchidos, verifique se o código chama o método **FetchAttributes** ou **FetchAttributesAsync**.

Os pares de nomes/valor de metadados são cabeçalhos HTTP válidos e, portanto, devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho HTTP válidos e identificadores C# válidos, podem conter apenas caracteres ASCII e devem ser tratados como insensíveis a casos. Os valores de metadados que contenham caracteres não ASCII devem ser codificados por Base64 ou codificados por URL.

## <a name="retrieve-container-properties"></a>Recuperar propriedades do contêiner

Para recuperar as propriedades do contêiner, ligue para um dos seguintes métodos:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

O exemplo de código a seguir busca as propriedades do sistema de um contêiner e grava alguns valores de propriedade em uma janela de console:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir metadados, adicione pares de valor de nome à coleta **de metadados** no recurso e, em seguida, chame um dos seguintes métodos para escrever os valores:

- [Setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

O nome dos metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores de C#. Os nomes de metadados preservam o caso com o qual foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados com o mesmo nome forem enviados para um recurso, o armazenamento Blob reatratam o código de erro HTTP 400 (Solicitação Ruim).

O exemplo de código a seguir define os metadados em um contêiner. Um valor é definido usando o método **Add** da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor. Ambos são válidos.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Para recuperar metadados, chame o método **FetchAttributes** ou **FetchAttributesAsync** no blob ou contêiner para preencher a coleção de **Metadados** e leia os valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Obter operação propriedades de contêiner](/rest/api/storageservices/get-container-properties)
- [Definir operação de metadados de contêiner](/rest/api/storageservices/set-container-metadata)
- [Obter operação de metadados de contêiner](/rest/api/storageservices/set-container-metadata)
