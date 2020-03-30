---
title: Criar ou excluir um recipiente blob com .NET - Armazenamento Azure
description: Saiba como criar ou excluir um contêiner blob em sua conta do Azure Storage usando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135931"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Crie ou exclua um contêiner no Azure Storage com .NET

Blobs no Azure Storage são organizados em contêineres. Antes de carregar uma bolha, você deve primeiro criar um recipiente. Este artigo mostra como criar e excluir contêineres com a [biblioteca cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Nomeie um recipiente

Um nome de contêiner deve ser um nome DNS válido, pois faz parte do URI exclusivo usado para abordar o contêiner ou suas bolhas. Siga estas regras ao nomear um contêiner:

- Os nomes dos contêineres podem ter entre 3 e 63 caracteres.
- Os nomes dos contêineres devem começar com uma letra ou número, e podem conter apenas letras minúsculas, números e o caractere dash (-).
- Dois ou mais caracteres de traço consecutivos não são permitidos em nomes de contêineres.

O URI para um contêiner está neste formato:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner, ligue para um dos seguintes métodos:

- [Criar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Os métodos **Criar** e **CriarAsync** lançam uma exceção se um contêiner com o mesmo nome já existir.

Os métodos **CreateIfNotExists** e **CreateIfNotAsync** retornam um valor booleano indicando se o contêiner foi criado. Se um contêiner com o mesmo nome já existe, então esses métodos retornam **False** para indicar que um novo contêiner não foi criado.

Os contêineres são criados imediatamente sob a conta de armazenamento. Não é possível aninhar um contêiner sob outro.

O exemplo a seguir cria um contêiner assíncronamente:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Criar o recipiente raiz

Um contêiner raiz funciona como um contêiner padrão para sua conta de armazenamento. Cada conta de armazenamento pode ter um recipiente raiz, que deve ser nomeado *$root.*. . Você deve criar ou excluir explicitamente o recipiente raiz.

Você pode referenciar uma bolha armazenada no recipiente raiz sem incluir o nome do recipiente raiz. O contêiner raiz permite que você faça referência a uma bolha no nível superior da hierarquia da conta de armazenamento. Por exemplo, faça referência a um blob que resida no contêiner raiz da seguinte maneira:

`https://myaccount.blob.core.windows.net/default.html`

O exemplo a seguir cria o recipiente raiz sincronicamente:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Excluir um contêiner

Para excluir um contêiner em .NET, use um dos seguintes métodos:

- [Excluir](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [ExcluirSeexiste](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Os métodos **Excluir** e **ExcluirAsync** lançam uma exceção se o contêiner não existir.

Os **métodos DeleteIfExist** e **DeleteIfAsync** retornam um valor booleano indicando se o contêiner foi excluído. Se o recipiente especificado não existir, então esses métodos retornam **Falsos** para indicar que o recipiente não foi excluído.

Depois de excluir um contêiner, você não pode criar um recipiente com o mesmo nome por pelo menos 30 segundos, e possivelmente mais tempo. Enquanto o contêiner estiver sendo excluído, uma tentativa de criar um contêiner com o mesmo nome falhará com o código de erro HTTP 409 (Conflito). Quaisquer outras operações no contêiner ou nas bolhas que ele contém falharão com o código de erro HTTP 404 (Não encontrado) enquanto o contêiner estiver sendo excluído.

O exemplo a seguir exclui o contêiner especificado e lida com a exceção se o contêiner não existir:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

O exemplo a seguir mostra como excluir todos os contêineres que começam com um prefixo especificado. O exemplo quebra o contrato de locação se houver uma locação existente no contêiner.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Confira também

- [Criar operação de contêineres](/rest/api/storageservices/create-container)
- [Operação Excluir Contêiner](/rest/api/storageservices/delete-container)
