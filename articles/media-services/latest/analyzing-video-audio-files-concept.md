---
title: Analisar arquivos de vídeo e áudio
titleSuffix: Azure Media Services
description: Saiba como analisar o conteúdo de áudio e vídeo usando AudioAnalyzerPreset e VideoAnalyzerPreset no Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269881"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analisar arquivos de vídeo e áudio com o Azure Media Services

O Azure Media Services v3 permite extrair insights de seus arquivos de vídeo e áudio com o Indexador de Vídeo. Este artigo descreve as predefinições do analisador v3 do Media Services usadas para extrair esses insights. Se você quiser informações mais detalhadas, use o Video Indexer diretamente. Para entender quando usar o analisador de vídeo indexador vs. media services predefinições, confira o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Para analisar seu conteúdo usando predefinições v3 do Media Services, você cria uma **Transformação** e envia um **Trabalho** que usa uma dessas predefinições: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. Para obter um tutorial que demonstre como usar **o VideoAnalyzerPreset,** consulte [Analisar vídeos com o Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Ao usar as predefinições do Video ou Audio Analyzer, use o portal do Azure para definir sua conta como 10 Unidades Reservada para Mídia S3. Para saber mais, confira [Processamento de mídia de escala](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você precisará respeitar todas as leis aplicáveis em seu uso do Video Indexer e não poderá usar o Video Indexer ou qualquer outro serviço do Azure de uma maneira que viole os direitos de outras pessoas ou que possa ser prejudicial a outras pessoas. Antes de carregar qualquer vídeo, incluindo dados biométricos, no serviço do Video Indexer para processamento e armazenamento, você precisará ter todos os direitos apropriados, incluindo todos os consentimentos apropriados, dos indivíduos mostrados no vídeo. Para saber mais sobre a conformidade, a privacidade e a segurança do Video Indexer, leia os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) da Microsoft. Para saber mais sobre as obrigações de privacidade e o tratamento de seus dados pela Microsoft, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement), o “OST” [(Termos do Online Services)](https://www.microsoft.com/licensing/product-licensing/products) e o “DPA” [(Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Informações de privacidade adicionais, incluindo retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao usar o Video Indexer, você concorda em vincular-se aos Termos dos Serviços Cognitivos, ao OST, ao DPA e à política de privacidade.

## <a name="built-in-presets"></a>Predefinições internas

O Serviços de Mídia do Microsoft Azure atualmente suporta as seguintes predefinições do analisador interno:  

|**Nome da predefinição**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisar áudio|A predefinição aplica um conjunto predefinido de operações de análise baseadas em IA, incluindo transcrição de fala. Atualmente, a predefinição dá suporta ao processamento de conteúdo com uma única faixa do áudio que contenha fala em um único idioma. É possível especificar o idioma o conteúdo de áudio na entrada usando o formato BCP-47 de 'marca de idioma-região'. Os idiomas suportados são inglês ('en-US' e 'en-GB'), espanhol ('es-ES' e 'es-MX'), francês ('fr-FR'), italiano ('it-IT'), japonês ('ja-JP'), português ('pt-BR'), chinês ('zh-CN'), alemão ('de-DE'), árabe ('ar-EG' e 'ar-SY'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru-RU'), russo ('ru Hindi ('hi-IN'), e coreano ('ko-KR').<br/><br/> Se o idioma não for especificado ou definido como nulo, a detecção automática do idioma escolhe o primeiro idioma detectado e continua com o idioma selecionado durante a duração do arquivo. O recurso de detecção automática de idioma suporta atualmente inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Ele não suporta a troca dinamicamente entre idiomas depois que a primeira língua é detectada. O recurso de detecção automática de idioma funciona melhor com gravações de áudio com fala claramente discernível. Se a detecção automática do idioma não encontrar o idioma, a transcrição volta para o inglês.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisar áudio e vídeo|Extraia insights (metadados avançados) de áudio e vídeo e gere um arquivo no formato JSON. É possível especificar se deseja extrair apenas insights de áudio ao processar um arquivo de vídeo. Para obter mais informações, consulte [Analisar vídeo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Detectando rostos presentes em vídeo|Descreve as configurações a serem usadas ao analisar um vídeo para detectar todos os rostos presentes.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A predefinição permite extrair vários insights de áudio de um arquivo de áudio ou vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47). As informações de áudio incluem:

* **Transcrição de áudio**: Uma transcrição das palavras faladas com carimbos de data e hora. Vários idiomas são suportados.
* **Indexação do alto-falante**: Um mapeamento dos alto-falantes e das palavras faladas correspondentes.
* **Análise do sentimento da**fala : A saída da análise de sentimento realizada na transcrição do áudio.
* **Palavras-chave**: Palavras-chave que são extraídas da transcrição de áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A predefinição permite extrair vários insights de áudio e vídeo de um arquivo de vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do vídeo, e uma coleção de miniaturas. Essa predefinição também aceita uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) (que representa o idioma do vídeo) como propriedade. As informações de vídeos incluem todas as informações de áudio mencionadas acima, e os itens adicionais a seguir:

* **Rastreamento facial**: O tempo durante o qual os rostos estão presentes no vídeo. Cada rosto tem um face ID e uma coleção correspondente de miniaturas.
* **Texto visual**: O texto detectado através do reconhecimento óptico de caracteres. O texto é carimbado e também usado para extrair palavras-chave (além da transcrição de áudio).
* **Quadros-chave**: Uma coleção de quadros-chave extraídos do vídeo.
* **Moderação de conteúdo visual**: A parte dos vídeos sinalizados como adultos ou picantes na natureza.
* **Anotação**: Resultado da anotação dos vídeos com base em um modelo de objeto pré-definido

## <a name="insightsjson-elements"></a>Elementos insights.json

A saída inclui um arquivo JSON (insights.json) com todos os insights encontrados no vídeo ou áudio. O JSON pode conter os seguintes elementos:

### <a name="transcript"></a>transcript

|Nome|Descrição|
|---|---|
|id|A ID da linha.|
|text|A própria transcrição.|
|Linguagem|O idioma da transcrição. Tem o objetivo dar suporte à transcrição na qual cada linha pode ter um idioma diferente.|
|instances|Uma lista com os intervalos de tempo nos quais essa linha apareceu. Se a instância for transcrita, ela terá apenas 1 instância.|

Exemplo:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|Nome|Descrição|
|---|---|
|id|A ID da linha de OCR.|
|text|O texto de OCR.|
|confidence|A confiança do reconhecimento.|
|Linguagem|O idioma do OCR.|
|instances|Uma lista de intervalos de tempo nos quais essa OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>faces

|Nome|Descrição|
|---|---|
|id|A ID da face.|
|name|O nome da face. Pode ser 'Desconhecido #0', uma celebridade identificada, ou uma pessoa treinada por clientes.|
|confidence|A confiança de identificação da face.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|O ID da miniatura dessa face.|
|knownPersonId|O ID interno (se for uma pessoa conhecida).|
|referenceId|O Bing ID (se for uma celebridade do Bing).|
|referenceType|No momento, apenas Bing.|
|título|O título (se for uma celebridade — por exemplo, "CEO da Microsoft").|
|imageUrl|A URL da imagem, se for uma celebridade.|
|instances|Casos em que o rosto apareceu no intervalo de tempo dado. Cada ocorrência também tem uma thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>shots

|Nome|Descrição|
|---|---|
|id|A ID da captura.|
|keyFrames|Uma lista com os quadros-chave dentro da captura (cada um tem uma ID e uma lista de intervalos de tempo de instâncias). As instâncias de frames principais têm um campo thumbnailId com o ID de miniatura da keyFrame.|
|instances|Uma lista com os intervalos de tempo desta captura (as capturas têm apenas 1 instância).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>estatísticas

|Nome|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por alto-falante.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|O mais longo monólogo do orador. Se o orador tem silêncios dentro do monólogo está incluído. O silêncio no início e no final do monólogo é removido.|
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monólogo do locutor (sem o silêncio intermediário) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentiments

Os sentimentos são agregadas de acordo com seu campo sentimentType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|Nome|Descrição|
|---|---|
|id|A ID do sentimento.|
|averageScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimento - Neutral/positivo/negativo|
|instances|Uma lista com os intervalos de tempo nos quais esse sentimento apareceu.|
|sentimentType |O tipo pode ser 'Positivo', 'Neutro' ou 'Negativo'.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>rótulos

|Nome|Descrição|
|---|---|
|id|A ID do rótulo.|
|name|O nome do rótulo (por exemplo, "Computador", "TV").|
|Linguagem|O idioma do nome do rótulo (quando traduzido). BCP-47|
|instances|Uma lista de intervalos de tempo nos quais esse rótulo apareceu (um rótulo pode aparecer várias vezes). Cada instância tem um campo de confiança. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|id|A ID da palavra-chave.|
|text|O texto da palavra-chave.|
|confidence|A confiança do reconhecimento da palavra-chave.|
|Linguagem|O idioma da palavra-chave (quando traduzida).|
|instances|Uma lista de intervalos de tempo nos quais essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer encontrou para potencialmente ter conteúdo adulto. Se o visualContentModeração está vazio, não há conteúdo adulto que foi identificado.

Os vídeos que contêm conteúdo adulto ou atraente podem estar disponíveis apenas para visualização privada. Os usuários podem enviar uma solicitação para uma revisão `IsAdult` humana do conteúdo, nesse caso o atributo conterá o resultado da revisão humana.

|Nome|Descrição|
|---|---|
|id|A ID de moderação de conteúdo visual.|
|adultScore|A pontuação de conteúdo adulta (do moderador de conteúdo).|
|racyScore|A pontuação racista (de moderação de conteúdo).|
|instances|Uma lista de intervalos de tempo em que apareceu esse visual moderação de conteúdo.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Próximas etapas

[Tutorial: Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md)
