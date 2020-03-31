---
title: Faixas de áudio descritivas de sinal com a Azure Media Services v3 | Microsoft Docs
description: Siga os passos deste tutorial para carregar um arquivo, codificar o vídeo, adicionar faixas de áudio descritivas e transmitir seu conteúdo com o Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392183"
---
# <a name="signal-descriptive-audio-tracks"></a>Faixas de áudio descritivas de sinal

Você pode adicionar uma faixa de narração ao seu vídeo para ajudar clientes com deficiência visual a seguir a gravação do vídeo ouvindo a narração. No Media Services v3, você sinaliza faixas de áudio descritivas anotando a faixa de áudio no arquivo manifesto.

Este artigo mostra como codificar um vídeo, carregar um arquivo MP4 (codec AAC) somente em áudio contendo áudio descritivo no ativo de saída e editar o arquivo .ism para incluir o áudio descritivo.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Revisar [embalagem dinâmica](dynamic-packaging-overview.md).
- Revise o tutorial [de upload, codificação e streaming de vídeos.](stream-files-tutorial-with-api.md)

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele 

A função **CreateInputAsset** cria um novo [Ativo](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este **Ativo** é usado como entrada para o seu trabalho de codificação. Em Serviços de Mídia v3, a entrada para um **Trabalho** pode ser um **Ativo** ou o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. 

Se você quiser aprender como codificar a partir de uma URL HTTPS, consulte [este artigo](job-input-from-http-how-to.md) .  

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir realiza essas ações:

* Cria um **Ativo** 
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável no contêiner do ativo [no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Se você precisar passar o nome do ativo de entrada criado para `Name` outros métodos, certifique-se de usar a propriedade no objeto de `CreateInputAssetAsync`ativo devolvido, por exemplo, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Crie um ativo de saída para armazenar o resultado do trabalho de codificação

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação. A função a seguir mostra como criar um ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Se você precisar passar o nome do ativo de saída criado para `Name` outros métodos, certifique-se de usar a propriedade no objeto de `CreateIOutputAssetAsync`ativo devolvido, por exemplo, outputAsset.Name. 

No caso deste artigo, `outputAsset.Name` passe o `SubmitJobAsync` `UploadAudioIntoOutputAsset` valor para as funções.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Crie uma transformação e um trabalho que codifica o arquivo carregado

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Empregos](transform-concept.md). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmitir para uma variedade de dispositivos de iOS e Android. 

O exemplo a seguir cria uma transformação (se não existe).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A função a seguir submete um trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. Recomendamos o uso da Event Grid para esperar o trabalho ser concluído.

O trabalho geralmente passa pelos seguintes estados: **Agendado,** **Enfileirado,** **Processamento,** **Acabado** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

Para obter mais informações, consulte [Handling Event Grid eventos](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Faça upload do arquivo MP4 somente de áudio

Carregue o arquivo MP4 (codec AAC) somente de áudio adicional contendo áudio descritivo no ativo de saída.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Aqui está um exemplo de `UpoadAudioIntoOutputAsset` uma chamada para a função:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Editar o arquivo .ism

Quando seu trabalho de codificação for feito, o ativo de saída conterá os arquivos gerados pelo trabalho de codificação. 

1. No portal Azure, navegue até a conta de armazenamento associada à sua conta de Serviços de Mídia. 
1. Encontre o contêiner com o nome do seu ativo de saída. 
1. No contêiner, encontre o arquivo .ism e clique em **Editar blob** (na janela direita). 
1. Edite o arquivo .ism adicionando as informações sobre o arquivo MP4 (codec AAC) somente em áudio carregado contendo áudio descritivo e **pressione Salvar** quando feito.

    Para sinalizar as faixas de áudio descritivas, você precisa adicionar parâmetros de "acessibilidade" e "função" ao arquivo .ism. É sua responsabilidade definir esses parâmetros corretamente para sinalizar uma faixa de áudio como descrição de áudio. Por exemplo, `<param name="accessibility" value="description" />` `<param name="role" value="alternate" />` adicione e ao arquivo .ism para uma faixa de áudio específica, como mostrado no exemplo a seguir.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Obtenha um localizador de streaming

Depois que a codificação for concluída, a próxima etapa é tornar no vídeo no ativo de saída disponível para os clientes para reprodução. Você pode fazer isso em duas etapas: primeiro, crie um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, crie as URLs de streaming que os clientes podem usar. 

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após fazer as chamadas da API e durar até que seja excluído, a menos que você configure os horários opcionais de início e término. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar os detalhes desejados **StreamingPolicyName**. Neste exemplo, você estará transmitindo conteúdo in-the-clear (ou não criptografado) para que a política de streaming clara predefinida **(PredefinedStreamingPolicy.ClearStreamingOnly)** seja usada.

> [!IMPORTANT]
> Ao usar uma [Política de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. A conta de Serviço de Mídia tem uma cota para o número de entradas de Política de Streaming. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

O código a seguir pressupõe que você está chamando a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Ao passo que o exemplo deste tópico discute streaming, você pode usar a mesma chamada para criar um Localizador de Streaming para fornecimento de vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter URLs de streaming

Agora que o [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado em **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome do host do [Ponto de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) com o caminho do **Localizador de Streaming**. Neste exemplo, o *padrão do * **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

> [!NOTE]
> Neste método, é necessário o locatorName que foi usado durante a criação do **Localizador de Streaming** para o Ativo gerado.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testar com o Player de Mídia do Azure

Para testar o streaming, este artigo usa o Player de Mídia do Azure. 

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)Web e navegue para .
2. Na **URL:** caixa, cole um dos valores de URL de streaming que você tem do seu aplicativo. 
 
     Você poderá colar a URL no formato HLS, Dash ou Smooth e o Player de Mídia do Azure alternará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção. 

## <a name="next-steps"></a>Próximas etapas

[Analisar vídeos](analyze-videos-tutorial-with-api.md)
