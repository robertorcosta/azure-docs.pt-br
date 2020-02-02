---
title: Como usar a transcrição do lote – serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 8a53f1cfbde2f518848e7ef1104bf41ba4996961
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936401"
---
# <a name="how-to-use-batch-transcription"></a>Como usar a transcrição do lote

A transcrição do lote é ideal para transcrever uma grande quantidade de áudio no armazenamento. Usando a API REST dedicada, você pode apontar para arquivos de áudio com um URI de assinatura de acesso compartilhado (SAS) e receber os resultados de transcrição de forma assíncrona.

A API oferece transcrição de fala para texto assíncrona e outros recursos. Você pode usar a API REST para expor métodos para:

- Criar solicitações de processamento em lotes
- Consultar o status
- Baixar resultados de transcrição
- Excluir informações de transcrição do serviço

A API detalhada está disponível como um [documento do Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), com o título `Custom Speech transcriptions`.

Os trabalhos de transcrição do lote são agendados de acordo com a melhor iniciativa. Atualmente, não há nenhuma estimativa para quando um trabalho mudar para o estado em execução. Em carga normal do sistema, isso deve acontecer em minutos. Uma vez no estado de execução, a transcrição real é processada mais rápido do que o áudio em tempo real.

Ao lado da API fácil de usar, você não precisa implantar pontos de extremidade personalizados e não tem nenhum requisito de simultaneidade para observar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Assinatura

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](get-started.md).

>[!NOTE]
> Uma assinatura padrão (S0) para o serviço de fala é necessária para usar a transcrição do lote. Chaves de assinatura gratuita (F0) não funcionarão. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos acústicos ou de idioma, siga as etapas em [personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [projetar modelos de linguagem de personalização](how-to-customize-language-model.md). Para usar os modelos criados na transcrição do lote, você precisa de suas IDs de modelo. Você pode recuperar a ID do modelo ao inspecionar os detalhes do modelo. Um ponto de extremidade personalizado implantado não é necessário para o serviço de transcrição do lote.

## <a name="the-batch-transcription-api"></a>A API de transcrição em lote

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3 | PCM | 16-bit | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG | OPUS | 16-bit | 8 kHz ou 16 kHz, mono ou estéreo |

Para fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Para cada canal, um arquivo de resultado JSON está sendo criado. Os carimbos de data/hora gerados por expressão permitem que o desenvolvedor crie uma transcrição final ordenada.

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
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Propriedades de configuração

Use essas propriedades opcionais para configurar a transcrição:

| Parâmetro | Description |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `None`, o que desativa a filtragem de profanação, `Masked` que substitui a profanidade por asteriscos, `Removed` que remove todos os palavrões do resultado ou `Tags`, que adiciona tags de "profanidade". A configuração padrão é `Masked`. |
| `PunctuationMode` | Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores aceitos são `None`, o que desativa a pontuação, `Dictated` que implica pontuação explícita, `Automatic` que permite ao decodificador lidar com pontuação ou `DictatedAndAutomatic`, o que implica em sinais de pontuação ditados ou automáticos. |
| `AddWordLevelTimestamps` | Especifica se os carimbos de data/hora no nível da palavra devem ser adicionados à saída. Os valores aceitos são `true`, o que habilita os carimbos de data/hora no nível da palavra e `false` (o valor padrão) para desabilitá-los. |
| `AddSentiment` | Especifica um sentimentos que deve ser adicionado ao expressão. Os valores aceitos são `true` que habilita a expressão e a `false` (o valor padrão) para desabilitá-lo. |
| `AddDiarization` | Especifica que a análise de diarization deve ser executada na entrada que é esperada como canal mono contendo duas vozes. Os valores aceitos são `true` que habilita diarization e `false` (o valor padrão) para desabilitá-lo. Ele também requer que `AddWordLevelTimestamps` seja definido como true.|
|`TranscriptionResultsContainerUrl`|URL opcional com [SAS de serviço](../../storage/common/storage-sas-overview.md) para um contêiner gravável no Azure. O resultado será armazenado nesse contêiner.

### <a name="storage"></a>Armazenamento

A transcrição do lote dá suporte ao [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para leitura de áudio e gravação de transcrições no armazenamento.

## <a name="the-batch-transcription-result"></a>O resultado da transcrição do lote

Para áudio de entrada mono, um arquivo de resultado de transcrição está sendo criado. Para áudio de entrada estéreo, dois arquivos de resultado de transcrição estão sendo criados. Cada um tem essa estrutura:

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

O resultado contém estes formulários:

|Formulário|Conteúdo|
|-|-|
|`Lexical`|As palavras reais reconhecidas.
|`ITN`|Forma normalizada de texto inverso do texto reconhecido. Abreviações ("Doctor Smith" para "Dr Smith"), números de telefone e outras transformações são aplicadas.
|`MaskedITN`|O formulário em com mascaramento de profanação aplicado.
|`Display`|O formulário de exibição do texto reconhecido. Isso inclui a pontuação adicional e a capitalização.

## <a name="speaker-separation-diarization"></a>Separação de alto-falantes (Diarization)

Diarization é o processo de separar os alto-falantes em um pedaço de áudio. Nosso pipeline de lote dá suporte a diarization e é capaz de reconhecer dois alto-falantes em gravações de canal mono. O recurso não está disponível em gravações de estéreo.

Toda a saída de transcrição contém um `SpeakerId`. Se diarization não for usado, ele mostrará `"SpeakerId": null` na saída JSON. Para diarization, damos suporte a duas vozes, portanto os alto-falantes serão identificados como `"1"` ou `"2"`.

Para solicitar diarization, basta adicionar o parâmetro relevante na solicitação HTTP, conforme mostrado abaixo.

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

Os carimbos de data/hora de nível de palavra também teriam que ser ' ativados ', uma vez que os parâmetros na solicitação acima indicam.

## <a name="sentiment-analysis"></a>Análise de sentimento

O recurso de sentimentos estima as opiniões expressas no áudio. A opinião é expressa por um valor entre 0 e 1 para `Negative`, `Neutral`e `Positive` de sentimentos. Por exemplo, a análise de sentimentos pode ser usada em cenários de Call Center:

- Obtenha informações sobre a satisfação do cliente
- Obtenha informações sobre o desempenho dos agentes (equipe fazendo as chamadas)
- Localizar o ponto exato no tempo em que uma chamada levou uma direção negativa
- O que deu certo ao transformar uma chamada negativa em uma direção positiva
- Identificar o que os clientes gostam e o que eles não gostam sobre um produto ou um serviço

A contagem de sentimentos é pontuada por segmento de áudio com base no formato léxico. O texto inteiro dentro desse segmento de áudio é usado para calcular as opiniões. Nenhuma sentimentos agregada está sendo calculada para a transcrição inteira.

Um exemplo de saída JSON é semelhante ao seguinte:

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

## <a name="best-practices"></a>Práticas recomendadas

O serviço de transcrição pode lidar com um grande número de transcrições enviadas. Você pode consultar o status de suas transcrições por meio de um `GET` no [método transcrições](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Mantenha as informações retornadas para um tamanho razoável especificando o parâmetro `take` (algumas centenas). [Exclua as transcrições](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regularmente do serviço depois de recuperar os resultados. Isso garantirá respostas rápidas das chamadas de gerenciamento de transcrição.

## <a name="sample-code"></a>Código de exemplo

Exemplos completos estão disponíveis no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do subdiretório `samples/batch`.

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

## <a name="next-steps"></a>Próximos passos

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
