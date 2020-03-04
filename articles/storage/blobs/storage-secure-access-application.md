---
title: Proteger o acesso aos dados do aplicativo
titleSuffix: Azure Storage
description: Use tokens SAS, criptografia e HTTPS para proteger os dados do aplicativo na nuvem.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 1075c03820efba44ceb8dea28aff6302d2667cf2
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892423"
---
# <a name="secure-access-to-application-data"></a>Proteger o acesso aos dados do aplicativo

Este tutorial é a parte três de uma série. Aprenda a proteger o acesso à conta de armazenamento. 

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Usar tokens SAS para acessar imagens em miniatura
> * Ativar a criptografia no lado do servidor
> * Habilitar o transporte somente para HTTPS

O [Armazenamento de Blobs do Azure](../common/storage-introduction.md#blob-storage) fornece um serviço robusto para armazenar arquivos de aplicativos. Este tutorial estende o [tópico anterior][previous-tutorial] para mostrar como proteger o acesso à sua conta de armazenamento de um aplicativo Web. Quando você terminar as imagens são criptografadas e o aplicativo Web usa tokens SAS seguros para acessar as imagens em miniatura.

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você deve ter concluído o tutorial anterior de Armazenamento: [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos][previous-tutorial].

## <a name="set-container-public-access"></a>Configurar o acesso público ao contêiner

Nesta parte da série de tutoriais, os tokens SAS são usadas para acessar as miniaturas. Nesta etapa, você define o acesso público do contêiner de _miniaturas_ para `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar tokens SAS para miniaturas

Na parte um dessa série de tutoriais, o aplicativo Web estava mostrando imagens de um contêiner público. Nesta parte da série, você usa tokens de SAS (assinaturas de acesso compartilhado) para recuperar as imagens em miniatura. Os tokens SAS permitem que você forneça acesso restrito a um contêiner ou blob com base em IP, protocolo, intervalo de tempo ou direitos permitidos. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).

Neste exemplo, o repositório de código-fonte usa o branch `sasTokens`, que tem um exemplo de código atualizado. Exclua a implantação existente do GitHub com o [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Em seguida, configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

No comando a seguir, `<web-app>` é o nome do seu aplicativo Web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

O branch `sasTokens` do repositório atualiza o arquivo `StorageHelper.cs`. Ele substitui a tarefa `GetThumbNailUrls` com o exemplo de código abaixo. A tarefa atualizada recupera as URLs de miniatura definindo uma [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy) para especificar a hora de início, a hora de expiração e as permissões para o token SAS. Uma vez implantado, o aplicativo Web agora recupera as miniaturas com uma URL usando um token SAS. A tarefa atualizada é mostrada no exemplo a seguir:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

As classes, propriedades e métodos a seguir são usados na tarefa anterior:

|Classe  |propriedades| Métodos  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Resultados](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Permissões](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Criptografia no servidor

[A SSE (Criptografia do Serviço de Armazenamento) do Azure](../common/storage-service-encryption.md) ajuda a proteger seus dados. A SSE criptografa os dados em repouso, tratando da criptografia, descriptografia e gerenciamento de chaves. Todos os dados são criptografados usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, um dos codificadores de blocos mais potentes.

A SSE criptografa automaticamente os dados em todos os níveis de desempenho (Standard e Premium), em todos os modelos de implantação (Azure Resource Manager e Clássico) e em todos os serviços do Armazenamento do Azure (Blobs, Filas, Tabelas e Arquivos). 

## <a name="enable-https-only"></a>Habilitar o HTTPS apenas

Para garantir que as solicitações de dados de e para uma conta de armazenamento são seguras, você pode limitar solicitações para HTTPS apenas. Atualize o protocolo necessário da conta de armazenamento usando o comando [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Teste a conexão usando `curl` que usa o protocolo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Agora que a transferência segura é necessária, você pode receber a seguinte mensagem:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Próximas etapas

Na parte três da série, você aprendeu como proteger o acesso à conta de armazenamento, como:

> [!div class="checklist"]
> * Usar tokens SAS para acessar imagens em miniatura
> * Ativar a criptografia no lado do servidor
> * Habilitar o transporte somente para HTTPS

Avance para a parte quatro da série para saber como monitorar e resolver problemas de um aplicativo de armazenamento de nuvem.

> [!div class="nextstepaction"]
> [Monitorar e solucionar problemas de armazenamento de aplicativos de nuvem do aplicativo](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
