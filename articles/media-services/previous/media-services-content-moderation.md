---
title: Usar o Azure Media Content Moderator para detectar possível conteúdo adulto e erótico | Microsoft Docs
description: O processador de mídia Azure Media Moderator ajuda a detectar potenciais conteúdos adultos e picantes em vídeos.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914306"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Usar o Azure Media Content Moderator para detectar possível conteúdo adulto e erótico 

> [!NOTE]
> O processador de mídia **Azure Media Content Moderator** será aposentado. Para a data de aposentadoria, consulte o tópico [componentes legados.](legacy-components.md)

## <a name="overview"></a>Visão geral
O processador de mídia **Azure Media Content Moderator** (MP) permite que você use moderação assistida por máquina para seus vídeos. Por exemplo, você talvez queira detectar possível conteúdo adulto e erótico em vídeos e revisar o conteúdo sinalizado por suas equipes de moderação humanas.

O MP do **Azure Media Content Moderator** está atualmente em fase de Visualização.

Este artigo fornece detalhes sobre o **Azure Media Content Moderator** e mostra como usá-lo com o SDK dos Serviços de Mídia para .NET.

## <a name="content-moderator-input-files"></a>Arquivos de entrada do Content Moderator
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="content-moderator-output-files"></a>Arquivos de saída do Content Moderator
O resultado moderado no formato JSON inclui quadros-chave e capturas detectados automaticamente. Os quadros-chave são retornados com pontuações de confiança para o possível conteúdo adulto ou erótico. Também incluem um sinalizador booliano que indica se uma revisão é recomendada. O sinalizador de recomendação de revisão recebe valores com base nos limites internos para pontuações de conteúdo adulto ou erótico.

## <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

O trabalho produz um arquivo de saída JSON que contém metadados sobre capturas e quadros-chave detectados e se eles têm conteúdo adulto ou erótico.

O JSON de saída contém os seguintes elementos:

### <a name="root-json-elements"></a>Elementos raiz JSON

| Elemento | Descrição |
| --- | --- |
| version |A versão do Content Moderator. |
| escala de tempo |"Tiques" por segundo do vídeo. |
| deslocamento |A diferença de tempo para carimbos de data/hora. Na versão 1.0 das APIs de Vídeo, este valor sempre será 0. Esse valor pode ser alterado no futuro. |
| taxa de quadros |Quadros por segundo do vídeo. |
| width |A largura do quadro de vídeo de saída, em pixels.|
| height |A altura do quadro de vídeo de saída, em pixels.|
| totalDuration |A duração do vídeo de entrada em "tiques". |
| [fragmentos](#fragments-json-elements) |Os metadados são agrupados em segmentos diferentes, chamados fragmentos. Cada fragmento contém uma captura detectada automaticamente com início, duração, número de intervalo e evento(s). |

### <a name="fragments-json-elements"></a>Elementos JSON de fragmentos

|Elemento|Descrição|
|---|---|
| start |A hora de início do primeiro evento em "tiques". |
| duration |A duração do fragmento, em “tiques”. |
| intervalo |O intervalo de cada entrada de evento dentro do fragmento, em “tiques”. |
| [Eventos](#events-json-elements) |Cada evento representa um clipe e cada clipe contém quadros-chave detectados e rastreados dentro dessa duração de tempo. É uma matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é composta por 0 ou mais eventos que ocorreram nesse ponto no tempo.|

### <a name="events-json-elements"></a>Elementos JSON de eventos

|Elemento|Descrição|
|---|---|
| reviewRecommended | `true` ou `false` dependendo de **adultScore** ou **racyScore** exceder os limites internos. |
| adultScore | Pontuação de confiança para o possível conteúdo adulto, em uma escala de 0,00 a 0,99. |
| racyScore | Pontuação de confiança para o possível conteúdo erótico, em uma escala de 0,00 a 0,99. |
| índice | Índice do quadro em uma escala desde o primeiro quadro do índice até o último quadro do índice. |
|  timestamp | O local do quadro, em "tiques". |
| shotIndex | O índice da captura pai. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Exemplo de resultado e guia de início rápido para o Content Moderator

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com o **Azure Media Content Moderator**, é preciso especificar uma predefinição de configuração. A predefinição de configuração a seguir serve apenas para moderação de conteúdo.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Exemplo de código do .NET

O exemplo de código .NET a seguir usa o SDK dos Serviços de mídia .NET para executar uma tarefa do Content Moderator. Ele usa um ativo de serviços de mídia como a entrada que contém o vídeo a ser moderado.
Confira o [Guia de início rápido do Content Moderator para vídeos](../../cognitive-services/Content-Moderator/video-moderation-api.md) para ver o código-fonte completo e o projeto do Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [solução de revisão e moderação de vídeos](../../cognitive-services/Content-Moderator/video-moderation-human-review.md) do Content Moderator.

Confira o [Guia de início rápido para moderação de vídeos](../../cognitive-services/Content-Moderator/video-moderation-api.md) para ver o código-fonte completo e o projeto do Visual Studio. 

Saiba como gerar [revisões de vídeos](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) usando a saída moderada e [transcrições moderadas](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) no .NET.

Confira o [tutorial detalhado de moderação e revisão de vídeos](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md) do .NET. 
