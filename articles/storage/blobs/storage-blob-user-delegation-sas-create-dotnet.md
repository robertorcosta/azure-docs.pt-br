---
title: Use .NET para criar uma delegação de usuário SAS para um contêiner ou blob
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de usuários SAS com credenciais do Azure Active Directory usando a biblioteca de clientes .NET para armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371829"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Crie uma delegação de usuário SAS para um contêiner ou blob com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (Azure AD) para criar uma delegação de usuários SAS para um contêiner ou blob com a biblioteca cliente do Azure Storage para .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Atribuir funções RBAC para acesso a dados

Quando um principal de segurança do Azure AD tenta acessar dados blob, esse principal de segurança deve ter permissões para o recurso. Se o principal de segurança é uma identidade gerenciada no Azure ou uma conta de usuário Azure AD executando código no ambiente de desenvolvimento, o principal de segurança deve ser atribuído a uma função RBAC que concede acesso a dados blob no Azure Storage. Para obter informações sobre como atribuir permissões via RBAC, consulte a seção **intitulada Atribuir funções RBAC para direitos de acesso** em Autorizar o acesso a [blobs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes azure Identity do Azure Storage, consulte a seção intitulada **Authenticate with the Azure Identity library** in [Authorize access to blobs and filas with Azure Active Directory e identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="add-using-directives"></a>Adicionar diretivas using

Adicione as `using` seguintes diretivas ao seu código para usar as bibliotecas de clientes azure Identity e Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Obtenha uma credencial de token autenticada

Para obter uma credencial de token que seu código pode usar para autorizar solicitações ao Azure Storage, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

O seguinte trecho de código mostra como obter a credencial de token autenticada e usá-la para criar um cliente de serviço para armazenamento Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obter a chave de delegação do usuário

Toda SAS é assinada com uma chave. Para criar um SAS de delegação de usuário, você deve primeiro solicitar uma chave de delegação de usuário, que é então usada para assinar o SAS. A chave de delegação do usuário é análoga à chave da conta usada para assinar um SAS de serviço ou uma conta SAS, exceto que ela depende de suas credenciais azure AD. Quando um cliente solicita uma chave de delegação de usuário usando um token OAuth 2.0, o Azure Storage retorna a chave de delegação do usuário em nome do usuário.

Uma vez que você tenha a chave de delegação do usuário, você pode usar essa chave para criar qualquer número de assinaturas de acesso compartilhados da delegação do usuário, ao longo da vida útil da chave. A chave de delegação do usuário é independente do token OAuth 2.0 usado para adquiri-lo, de modo que o token não precisa ser renovado desde que a chave ainda seja válida. Você pode especificar que a chave é válida por um período de até 7 dias.

Use um dos seguintes métodos para solicitar a chave de delegação do usuário:

- [Chave de delegação getuser](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetuserDelegationKeyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

O seguinte trecho de código recebe a chave da delegação do usuário e escreve suas propriedades:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Crie o token SAS

O trecho de código a seguir mostra criar um novo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e fornecer os parâmetros para a delegação do usuário SAS. Em seguida, o trecho chama os [Parâmetros de ToSasQuery](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a seqüência de token SAS. Finalmente, o código constrói o URI completo, incluindo o endereço de recurso e o token SAS.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exemplo: Obtenha uma delegação de usuários SAS

O método de exemplo a seguir mostra o código completo para autenticar o principal de segurança e criar o SAS da delegação do usuário:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemplo: Leia uma bolha com uma delegação de usuários SAS

O exemplo a seguir testa o SAS da delegação do usuário criado no exemplo anterior a partir de um aplicativo cliente simulado. Se o SAS for válido, o aplicativo cliente poderá ler o conteúdo da bolha. Se o SAS for inválido, por exemplo, se ele tiver expirado, o Azure Storage retorna o código de erro 403 (Proibido).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Confira também

- [Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](../common/storage-sas-overview.md)
- [Obter operação chave da delegação do usuário](/rest/api/storageservices/get-user-delegation-key)
- [Crie uma delegação de usuários SAS (Rest API)](/rest/api/storageservices/create-user-delegation-sas)
