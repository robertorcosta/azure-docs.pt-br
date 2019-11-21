---
title: How to use Batch Transcription - Speech Service
titleSuffix: Azure Cognitive Services
description: A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 5418b378c2c3cff09dbccbaa7b7240c61bbb583e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221522"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição do lote?

A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Assinatura

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](get-started.md). Se você planeja obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo que você precisa fazer.

>[!NOTE]
> Uma assinatura standard (S0) para Serviços de Fala é necessária para usar a transcrição do lote. Chaves de assinatura gratuita (F0) não funcionarão. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos de linguagem ou acústicos, siga as etapas em [Personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [Personalizando modelos de linguagem](how-to-customize-language-model.md). Para usar os modelos criados na transcrição do lote, você precisa de suas IDs de modelo. Essa ID não é a ID do ponto de extremidade que você encontra na exibição de detalhes do ponto de extremidade, é a ID do modelo que pode ser recuperada quando você seleciona os detalhes dos modelos.

## <a name="the-batch-transcription-api"></a>A API de transcrição em lote

A API de Transcrição em Lote oferece transcrição de fala para texto assíncrona, juntamente com recursos adicionais. É uma API REST que expõe métodos para:

1. Criando solicitações de processamento em lote
1. Status da consulta
1. Fazer o download de transcrições

> [!NOTE]
> A API de Transcrição em Lote é ideal para call centers, que normalmente acumulam milhares de horas de áudio. Ela facilita a transcrição de grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| MP3 | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| OGG | OPUS | 16-bit | 8 ou 16 kHz, mono, estéreo |

Para fluxos de áudio estéreo, a API de transcrição de lotes divide os canais esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os timestamps por emissão permitem ao desenvolvedor criar uma transcrição final ordenada. Essa solicitação de exemplo inclui propriedades para a filtragem de conteúdo ofensivo, pontuação e carimbos de data/hora no nível da palavra.

### <a name="configuration"></a>Configuração

Parâmetros de configuração são fornecidos como JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> A API de Transcrição em Lote usa um serviço REST para solicitar transcrições, seus status e resultados associados. É possível usar a API de qualquer linguagem. A próxima seção descreve como a API é usada.

### <a name="configuration-properties"></a>Propriedades de configuração

Use these optional properties to configure transcription:

| . | Descrição |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `None`, o que desativa a filtragem de profanação, `masked` que substitui a profanidade por asteriscos, `removed` que remove todos os palavrões do resultado ou `tags`, que adiciona tags de "profanidade". A configuração padrão é `masked`. |
| `PunctuationMode` | Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores aceitos são `None`, o que desativa a pontuação, `dictated` que implica pontuação explícita, `automatic` que permite ao decodificador lidar com pontuação ou `dictatedandautomatic`, o que implica em sinais de pontuação ditados ou automáticos. |
 | `AddWordLevelTimestamps` | Especifica se os carimbos de data/hora no nível da palavra devem ser adicionados à saída. Os valores aceitos são `true`, o que habilita os carimbos de data/hora no nível da palavra e `false` (o valor padrão) para desabilitá-los. |
 | `AddSentiment` | Specifies sentiment should be added to the utterance. Accepted values are `true` which enables sentiment per utterance and `false` (the default value) to disable it. |
 | `AddDiarization` | Specifies that diarization analysis should be carried out on the input which is expected to be mono channel containing two voices. Accepted values are `true` which enables diarization and `false` (the default value) to disable it. It also requires `AddWordLevelTimestamps` to be set to true.|

### <a name="storage"></a>Armazenamento

Batch transcription supports [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) for reading audio and writing transcriptions to storage.

## <a name="webhooks"></a>Webhooks

Polling for transcription status may not be the most performant, or provide the best user experience. To poll for status, you can register callbacks, which will notify the client when long-running transcription tasks have completed.

For more details, see [Webhooks](webhooks.md).

## <a name="speaker-separation-diarization"></a>Speaker Separation (Diarization)

Diarization is the process of separating speakers in a piece of audio. Our Batch pipeline supports Diarization and is capable of recognizing two speakers on mono channel recordings.

To request that your audio transcription request is processed for diarization, you simply have to add the relevant parameter in the HTTP request as shown below.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Word level timestamps would also have to be 'turned on' as the parameters in the above request indicate.

The corresponding audio will contain the speakers identified by a number (currently we support only two voices, so the speakers will be identified as 'Speaker 1 'and 'Speaker 2') followed by the transcription output.

Also note that Diarization is not available in Stereo recordings. Furthermore, all JSON output will contain the Speaker tag. If diarization is not used, it will show 'Speaker: Null' in the JSON output.

> [!NOTE]
> Diarization is available in all regions and for all locales!

## <a name="sentiment"></a>Sentimento

Sentiment is a new feature in Batch Transcription API and is an important feature in the call center domain. Customers can use the `AddSentiment` parameters to their requests to

1.  Get insights on customer satisfaction
2.  Get insight on the performance of the agents (team taking the calls)
3.  Pinpoint the exact point in time when a call took a turn in a negative direction
4.  Pinpoint what went well when turning negative calls to positive
5.  Identify what customers like and what they dislike about a product or a service

Sentiment is scored per audio segment where an audio segment is defined as the time lapse between the start of the utterance (offset) and the detection silence of end of byte stream. The entire text within that segment is used to calculate sentiment. We DO NOT calculate any aggregate sentiment values for the entire call or the entire speech of each channel. These aggregations are left to the domain owner to further apply.

Sentiment is applied on the lexical form.

A JSON output sample looks like below:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
The feature uses a Sentiment model, which is currently in Beta.

## <a name="sample-code"></a>Código de exemplo

Complete samples are available in the [GitHub sample repository](https://aka.ms/csspeech/samples) inside the `samples/batch` subdirectory.

Você precisa personalizar o código de exemplo com as informações de assinatura, a região de serviço, o URI de SAS que aponta para o arquivo de áudio a transcrever e IDs de modelo caso deseje usar um modelo de linguagem ou acústico personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código de exemplo configurará o cliente e enviará a solicitação de transcrição. Em seguida, ele sondará informações de status e imprimirá os detalhes sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para detalhes completos sobre as chamadas anteriores, confira o nosso [documento Swagger](https://westus.cris.ai/swagger/ui/index). Para o exemplo completo mostrado aqui, acesse [GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.

Anote a configuração assíncrona de áudio de lançamento e receber o status de transcrição. O cliente que você cria é um cliente HTTP .NET. Há um método `PostTranscriptions` para enviar os detalhes do arquivo de áudio e um método `GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou arquivos. Para especificar os modelos, você pode passar o mesmo método que os IDs de modelo para o modelo acústico e de idioma.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para os modelos de linha de base. Se você especificar apenas um idioma, a ID de modelo (e nenhuma ID de modelo acústico), um modelo acústico correspondente será selecionado automaticamente. Se você especificar apenas uma ID de modelo acústico, um modelo de idioma correspondente será selecionado automaticamente.

## <a name="download-the-sample"></a>Baixar o exemplo

Você pode encontrar o exemplo no diretório `samples/batch` do [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Trabalhos em lotes de transcrição são agendados por melhor esforço, não há nenhum tempo estimado para quando um trabalho será alterado para o estado de execução. Uma vez no estado de execução, a transcrição real é processada mais rapidamente do que o áudio em tempo real.

## <a name="next-steps"></a>Próximos passos

* [Obter a assinatura de avaliação do Speech](https://azure.microsoft.com/try/cognitive-services/)
