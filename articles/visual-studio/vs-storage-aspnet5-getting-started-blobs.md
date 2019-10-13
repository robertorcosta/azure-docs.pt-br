---
title: Introdução ao armazenamento de BLOBs usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o Armazenamento de Blobs do Azure em um projeto do ASP.NET Core no Visual Studio após a criação de uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298841"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Este artigo descreve como começar a usar o armazenamento de Blobs do Azure no Visual Studio após a criação ou referência de uma conta de armazenamento do Azure em um projeto do ASP.NET Core usando o recurso **Serviços Conectados** do Visual Studio. A operação **Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto. (Veja a [documentação do Armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento do Azure).

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Blobs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos. Este artigo descreve como começar a usar o armazenamento de blobs depois de criar uma conta de armazenamento do Azure usando o recurso **Serviços Conectados** do Visual Studio em um projeto do ASP.NET Core.

Assim como arquivos residem em pastas, blobs de armazenamento residem em contêineres. Após ter criado um blob, crie um ou mais contêineres nesse blob. Por exemplo, em um blob chamado "Scrapbook", você pode criar contêineres chamados "imagens" para armazenar fotos e "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você poderá carregar arquivos individuais para eles. Confira [Início Rápido: Carregar, baixar e listar BLOBs usando. NET @ no__t-0 para obter mais informações sobre como manipular BLOBs programaticamente.

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que os métodos assíncronos estejam sendo usados. Confira [Programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para saber mais.

## <a name="access-blob-containers-in-code"></a>Acessar contêineres de blob em código

Para acessar programaticamente blobs em projetos do ASP.NET Core, você precisará adicionar o código a seguir, se ainda não existir:

1. Adicione as instruções `using` necessárias:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obtenha um objeto `CloudStorageAccount` que represente as informações da conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações sobre a conta de armazenamento da configuração do serviço do Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Use um objeto `CloudBlobClient` para obter uma referência do `CloudBlobContainer` a um contêiner existente na sua conta de armazenamento:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Criar um contêiner no código

Você também pode usar o `CloudBlobClient` para criar um contêiner na sua conta de armazenamento chamando `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Se quiser tornar os arquivos dentro do contêiner disponíveis a todos, defina o contêiner como público:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Para carregar um arquivo de blob para um contêiner, obtenha uma referência de contêiner e use-a para obter uma referência de blob. Em seguida, carregue qualquer fluxo de dados para essa referência chamando o método `UploadFromStreamAsync`. Essa operação criará o blob, caso ele não exista, e substituirá o blob existente. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, primeiro obtenha a referência de um contêiner, chame seu método `ListBlobsSegmentedAsync` para recuperar os blobs e/ou diretórios dentro dele. Para acessar um conjunto sofisticado de propriedades e métodos para um `IListBlobItem` retornado, converta-o em um objeto `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. Se o tipo de blob for desconhecido, use uma verificação de tipo para determinar em qual outro tipo convertê-lo.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Confira [Início Rápido: Carregar, baixar e listar BLOBs usando. NET @ no__t-0 para outras maneiras de listar o conteúdo de um contêiner de BLOB.

## <a name="download-a-blob"></a>Baixar um blob

Para baixar um blob, obtenha primeiro uma referência ao blob e depois chame o método `DownloadToStreamAsync`. O exemplo a seguir usa o método `DownloadToStreamAsync` para transferir o conteúdo do blob para um objeto de fluxo que pode então ser salvo como um arquivo local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Confira [Início Rápido: Carregar, baixar e listar BLOBs usando. NET @ no__t-0 para outras maneiras de salvar BLOBs como arquivos.

## <a name="delete-a-blob"></a>Excluir um blob

Para excluir um blob, obtenha primeiro uma referência ao blob e depois chame o método `DeleteAsync`:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
