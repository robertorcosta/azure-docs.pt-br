---
title: O que é transcrição em lote - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 1f88df186526c2f9903337bb3331940be0989c3d
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892454"
---
# <a name="what-is-batch-transcription"></a>O que é transcrição do lote?

Transcrição em lote é um conjunto de operações de API REST que permite transcrever uma grande quantidade de áudio no armazenamento. Você pode apontar para arquivos de áudio com um URI de assinatura de acesso compartilhado (SAS) e receber resultados de transcrição assíncronamente.

A transcrição assíncrona de fala para texto é apenas uma das características. Você pode usar APIs REST de transcrição em lote para chamar os seguintes métodos:



|    Operação de transcrição em lote                                             |    Método    |    Chamada de API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Cria uma nova transcrição.                                              |    POST      |    api/speechtotext/v2.0/transcrições            |
|    Recupera uma lista de transcrições para a assinatura autenticada.    |    GET       |    api/speechtotext/v2.0/transcrições            |
|    Recebe uma lista de locais suportados para transcrições offline.              |    GET       |    api/speechtotext/v2.0/transcrições/localidades    |
|    Atualiza os detalhes mutáveis da transcrição identificada pelo seu ID.    |    PATCH     |    api/speechtotext/v2.0/transcrições/{id}       |
|    Exclui a tarefa de transcrição especificada.                                 |    Delete (excluir)    |    api/speechtotext/v2.0/transcrições/{id}       |
|    A transcrição é identificada pela identificação.                        |    GET       |    api/speechtotext/v2.0/transcrições/{id}       |




Você pode revisar e testar a API detalhada, que está `Custom Speech transcriptions`disponível como um [documento Swagger,](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)sob o título .

Os trabalhos de transcrição em lote são agendados com o melhor esforço. Atualmente, não há estimativa de quando um emprego muda para o estado em execução. Sob carga normal do sistema, deve acontecer em poucos minutos. Uma vez no estado de execução, a transcrição real é processada mais rápido do que o áudio em tempo real.

Ao lado da API fácil de usar, você não precisa implantar pontos finais personalizados e não tem requisitos de concorrência para observar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Assinatura

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](get-started.md).

>[!NOTE]
> Uma assinatura padrão (S0) para o serviço Speech é necessária para usar a transcrição em lote. As chaves de assinatura gratuitas (F0) não funcionam. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos acústicos ou linguísticos, siga os passos em [Personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [modelos de linguagem de personalização de design](how-to-customize-language-model.md). Para usar os modelos criados na transcrição em lote, você precisa de seus IDs de modelo. Você pode recuperar o ID do modelo quando inspecionar os detalhes do modelo. Um ponto final personalizado implantado não é necessário para o serviço de transcrição em lote.

## <a name="the-batch-transcription-api"></a>A API de transcrição em lote

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bitrate | Taxa de amostragem                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bit  | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3    | PCM   | 16-bit  | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG    | OPUS  | 16-bit  | 8 kHz ou 16 kHz, mono ou estéreo |

Para fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Para cada canal, um arquivo de resultado JSON está sendo criado. Os carimbos de tempo gerados por enunciado permitem que o desenvolvedor crie uma transcrição final ordenada.

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

:::row:::
   :::column span="1":::
      **Parâmetro**
   :::column-end:::
   :::column span="2":::
      **Descrição**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos `None` são desabilitar `Masked` a filtragem de palavrões, `Removed` substituir a profanação por asteriscos, remover todos os palavrões do resultado ou `Tags` adicionar tags de "profanação". A configuração padrão é `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores `None` aceitos são desabilitar a pontuação, `Dictated` implicar pontuação `Automatic` explícita (falada), deixar que o `DictatedAndAutomatic` decodificador lide com pontuação, ou usar pontuação ditada e automática. A configuração padrão é `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Especifica se os carimbos de data/hora no nível da palavra devem ser adicionados à saída. Os valores aceitos são `true` para `false` habilitar os carimbos de tempo do nível de palavra e (o valor padrão) desabilitá-lo.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Especifica se a análise de sentimento deve ser aplicada ao enunciado. Os valores aceitos são `true` para habilitar e `false` (o valor padrão) desabilitá-lo. Consulte [Análise de Sentimentos](#sentiment-analysis) para obter mais detalhes.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Especifica que a análise de diarização deve ser realizada na entrada, que deverá ser um canal mono contendo duas vozes. Os valores aceitos `true` estão `false` permitindo a diarização e (o valor padrão) para desativá-lo. Também precisa `AddWordLevelTimestamps` ser definido como verdadeiro.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL opcional com [serviço SAS](../../storage/common/storage-sas-overview.md) para um contêiner gravável no Azure. O resultado é armazenado neste recipiente.
:::row-end:::

### <a name="storage"></a>Armazenamento

A transcrição em lote suporta [o armazenamento do Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para leitura de áudio e transcrição de gravação para armazenamento.

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
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
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
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
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

:::row:::
   :::column span="1":::
      **Formulário**
   :::column-end:::
   :::column span="2":::
      **Conteúdo**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      As palavras reais reconhecidas.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Forma inversa-texto-normalizada do texto reconhecido. Abreviações ("doctor smith" para "dr smith"), números de telefone e outras transformações são aplicadas.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      O formulário ITN com máscara de profanação aplicado.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      A forma de exibição do texto reconhecido. Pontuação adicional e capitalização estão incluídas.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separação dos alto-falantes (Diarização)

Diarização é o processo de separação de alto-falantes em um pedaço de áudio. Nosso pipeline Batch suporta a diarização e é capaz de reconhecer dois alto-falantes em gravações de canal mono. O recurso não está disponível em gravações estéreo.

Toda a saída `SpeakerId`de transcrição contém um . Se a diarização não `"SpeakerId": null` for usada, ela será mostrada na saída JSON. Para a diarização apoiamos duas vozes, para `"1"` `"2"`que os alto-falantes sejam identificados como ou .

Para solicitar a diarização, basta adicionar o parâmetro relevante na solicitação HTTP, conforme mostrado abaixo.

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

Os carimbos de tempo no nível da palavra também teriam que ser 'ligados' como os parâmetros na solicitação acima indicam.

## <a name="sentiment-analysis"></a>Análise de sentimento

O recurso de sentimento estima o sentimento expresso no áudio. O sentimento é expresso por um `Negative` `Neutral`valor `Positive` entre 0 e 1 para , e sentimento. Por exemplo, a análise de sentimento pode ser usada em cenários de call center:

- Obtenha insights sobre a satisfação do cliente
- Obtenha informações sobre o desempenho dos agentes (equipe que recebe as chamadas)
- Encontre o ponto exato no tempo quando uma chamada tomou uma volta em uma direção negativa
- O que deu certo ao transformar uma chamada negativa em uma direção positiva
- Identifique o que os clientes gostam e o que eles não gostam em um produto ou serviço

O sentimento é pontuado por segmento de áudio com base na forma léxica. Todo o texto dentro desse segmento de áudio é usado para calcular o sentimento. Nenhum sentimento agregado está sendo calculado para toda a transcrição. Atualmente, a análise de sentimentos está disponível apenas para a língua inglesa.

> [!NOTE]
> Recomendamos o uso da API do Microsoft Text Analytics. Ele oferece recursos mais avançados além da análise de sentimentos, como extração de frases-chave, detecção automática de linguagem e muito mais. Você pode encontrar informações e amostras na [documentação text analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

Uma amostra de saída JSON parece abaixo:

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

O serviço de transcrição pode lidar com um grande número de transcrições submetidas. Você pode consultar o status de suas `GET` transcrições através de um no [método de transcrições](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Mantenha as informações devolvidas a um `take` tamanho razoável especificando o parâmetro (algumas centenas). [Exclua transcrições](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regularmente do serviço assim que você recuperar os resultados. Isso garante respostas rápidas das chamadas de gerenciamento de transcrição.

## <a name="sample-code"></a>Código de exemplo

As amostras completas estão disponíveis no [repositório de amostras do GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Você precisa personalizar o código de exemplo com as informações de assinatura, a região de serviço, o URI de SAS que aponta para o arquivo de áudio a transcrever e IDs de modelo caso deseje usar um modelo de linguagem ou acústico personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código de amostra configura o cliente e envia a solicitação de transcrição. Em seguida, ele pesquisa as informações de status e imprimir detalhes sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para obter detalhes completos sobre as chamadas anteriores, consulte nosso [documento Swagger](https://westus.cris.ai/swagger/ui/index). Para o exemplo completo mostrado aqui, acesse [GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.

Anote a configuração assíncrona de áudio de lançamento e receber o status de transcrição. O cliente que você cria é um cliente HTTP .NET. Há um método `PostTranscriptions` para enviar os detalhes do arquivo de áudio e um método `GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou arquivos. Para especificar os modelos, você pode passar o mesmo método que os IDs de modelo para o modelo acústico e de idioma.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para os modelos de linha de base. Se você especificar apenas um idioma, a ID de modelo (e nenhuma ID de modelo acústico), um modelo acústico correspondente será selecionado automaticamente. Se você especificar apenas uma ID de modelo acústico, um modelo de idioma correspondente será selecionado automaticamente.

## <a name="download-the-sample"></a>Baixar o exemplo

Você pode encontrar o exemplo no diretório `samples/batch` do [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
