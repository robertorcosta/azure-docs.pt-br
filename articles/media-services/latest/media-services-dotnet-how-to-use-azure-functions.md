---
title: Desenvolver Azure Functions com os serviços de mídia v3
description: Este tópico mostra como começar a desenvolver Azure Functions com os serviços de mídia v3 usando o portal do Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e0bb8ccf3be6038c228034a55cd15cadcebddbb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572321"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Desenvolver Azure Functions com os serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como começar a criação de Azure Functions que usam os Serviços de Mídia. A Azure Function definida neste artigo monitora um contêiner de conta de armazenamento chamado **input** para novos arquivos MP4. Depois que um arquivo for solto no contêiner de armazenamento, o gatilho de blob executará a função. Para examinar Azure Functions, consulte  [visão geral](../../azure-functions/functions-overview.md) e outros tópicos na seção **Azure Functions** .

Se você quiser explorar e implantar as Azure Functions existentes que usam o Serviços de Mídia do Azure, confira [Azure Functions dos Serviços de Mídia](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Esse repositório contém exemplosque usam os Serviços de Mídia para mostrar os fluxos de trabalho relativos à ingestão de conteúdo diretamente do armazenamento de blobs, à codificação e à gravação do conteúdo de volta no armazenamento de blobs.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de criar sua primeira função, você precisará ter uma conta ativa do Azure. Se você ainda não tiver uma conta do Azure, [há contas gratuitas disponíveis](https://azure.microsoft.com/free/).
- Se você pretende criar Azure Functions que executam ações em sua conta do AMS (Serviços de Mídia do Azure) ou escutar eventos enviados pelos Serviços de Mídia, crie uma conta do AMS conforme descrito [aqui](create-account-howto.md).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

1. Vá para o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Crie um aplicativo de função conforme descrito [aqui](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Uma conta de armazenamento que você especificar deve estar na mesma região que seu aplicativo.

## <a name="configure-function-app-settings"></a>Definir configurações do aplicativo de funções

Ao desenvolver funções dos Serviços de Mídia, é útil adicionar variáveis de ambiente que serão usadas em todas as funções. Para definir as configurações de aplicativo, clique no link Definir configurações de aplicativo. Para obter mais informações, consulte [Como definir configurações de aplicativo de uma Azure Function](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-a-function"></a>Criar uma função

Depois que o aplicativo de funções for implantado, você poderá encontrá-lo entre os **Serviços de Aplicativos** do Azure Functions.

1. Selecione seu aplicativo de funções e clique em **Nova Função**.
1. Escolha a linguagem **C#** e o cenário **Processamento de Dados**.
1. Escolha o modelo **BlobTrigger**. Essa função será disparada sempre que um blob for carregado no contêiner **input**. O nome de **input** é especificado no **Caminho**, na próxima etapa.
1. Depois de selecionar **BlobTrigger**, alguns outros controles serão exibidos na página.
1. Clique em **Criar**.

## <a name="files"></a>Arquivos

A Azure Function está associada a arquivos de código e a outros arquivos descritos nesta seção. Quando você usar o Portal do Azure para criar uma função, **function.json** e **run.csx** são criadas para você. Você precisa adicionar ou carregar um arquivo **project.json**. O restante desta seção mostra uma breve explicação de cada arquivo e mostra as respectivas definições.

### <a name="functionjson"></a>function.json

O arquivo function.json define as associações de função e outras definições de configuração. O runtime usa esse arquivo para determinar os eventos a serem monitorados, bem como para passar e retornar dados da execução da função. Para obter mais informações, consulte [Azure Functions associações http e webhook](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Defina a propriedade **disabled** como **true** para impedir que a função seja executada.

Substitua o conteúdo do arquivo function.json existente pelo seguinte código:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

O arquivo project.json contém dependências. Aqui está um exemplo de **project.jsno** arquivo que inclui os pacotes de serviços de mídia do .net Azure necessários do NuGet. Observe que os números de versão serão alterados com as últimas atualizações nos pacotes; portanto, é necessário confirmar as versões mais recentes.

Adicione a definição a seguir a project.json.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```

### <a name="runcsx"></a>run.csx

Este é o código C# para sua função.  A função definida abaixo monitora um contêiner de conta de armazenamento chamado **input** (que é o que foi especificado no caminho) para verificar se há novos arquivos MP4. Depois que um arquivo for solto no contêiner de armazenamento, o gatilho de blob executará a função.

O exemplo definido nesta seção demonstra:

1. Como ingerir um ativo em uma conta dos serviços de mídia (por meio do coping de um blob em um ativo do AMS)
2. Como enviar um trabalho de codificação que usa a predefinição "streaming adaptável" do Media Encoder Standard

Substitua o conteúdo do arquivo run.csx existente pelo seguinte código: depois de ter definindo sua função, clique em **Salvar e executar**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testar a função

Para testar a função, você precisa carregar um arquivo MP4 no contêiner **input** da conta de armazenamento especificada na cadeia de conexão.  

1. Selecione a conta de armazenamento que você especificou.
2. Clique em **BLOBs**.
3. Clique em **+ Contêiner**. Nomeie o contêiner **entrada**.
4. Pressione **Carregar** e navegue até um arquivo .mp4 que você deseje carregar.

>[!NOTE]
> Ao usar um gatilho de blob em um plano de Consumo, pode haver um atraso de até 10 minutos no processamento de novos blobs depois que um aplicativo de funções ficar ocioso. Depois que o aplicativo de funções estiver em execução, os blobs serão processados imediatamente. Para obter mais informações, consulte [Gatilhos e associações de armazenamento de blobs](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia.

Para obter mais detalhes e exemplos completos/soluções de como usar Azure Functions e aplicativos lógicos com os serviços de mídia do Azure para criar fluxos de trabalho de criação de conteúdo personalizado, consulte os [serviços de mídia Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)
