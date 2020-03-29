---
title: Especifique uma chave fornecida pelo cliente em uma solicitação de armazenamento Blob com .NET - Azure Storage
description: Saiba como especificar uma chave fornecida pelo cliente em uma solicitação de armazenamento Blob usando .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806993"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especifique uma chave fornecida pelo cliente em uma solicitação de armazenamento Blob com .NET

Os clientes que fazem solicitações contra o armazenamento Azure Blob têm a opção de fornecer uma chave de criptografia em uma solicitação individual. A inclusão da chave de criptografia na solicitação fornece controle granular sobre as configurações de criptografia para operações de armazenamento Blob. As chaves fornecidas pelo cliente (visualização) podem ser armazenadas no Azure Key Vault ou em outra loja-chave.

Este artigo mostra como especificar uma chave fornecida pelo cliente em uma solicitação com .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes azure Identity do Azure Storage, consulte a seção intitulada **Authenticate with the Azure Identity library** in [Authorize access to blobs and filas with Azure Active Directory e identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Exemplo: Use uma chave fornecida pelo cliente para carregar uma bolha

O exemplo a seguir cria uma chave fornecida pelo cliente e usa essa chave para carregar uma bolha. O código faz upload de um bloco e, em seguida, compromete a lista de blocos para escrever a bolha no Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
- [Autorize o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md)
