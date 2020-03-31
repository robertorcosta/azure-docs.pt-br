---
title: Encode custom transform usando Media Services v3 REST - Azure | Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761806"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Como codificar com uma transformação personalizada - REST

Ao codificar com o Azure Media Services, você pode começar rapidamente com uma das predefinições incorporadas recomendadas, com base nas práticas recomendadas do setor, como demonstrado no tutorial [de arquivos de Streaming.](stream-files-tutorial-with-rest.md#create-a-transform) Você também pode construir uma predefinição personalizada para atingir seus requisitos específicos de cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores para altura e largura no conteúdo AVC devem ser um múltiplo de 4.
* No Azure Media Services v3, todas as bitrates de codificação estão em bits por segundo. Isto é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 foi especificada como 128 (kilobits/segundo), em v3 seria definida como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). <br/>Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).<br/>Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Defina uma predefinição personalizada

O exemplo a seguir define o corpo de solicitação de uma nova Transformação. Definimos um conjunto de saídas que queremos gerar quando este Transform for usado. 

Neste exemplo, adicionamos primeiro uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos rótulos para que possam ser usados nos nomes dos arquivos de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas em 50% da resolução do vídeo de entrada, e em três carimbos de tempo - {25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, especificamos o formato para os arquivos de saída - um para vídeo + áudio e outro para as miniaturas. Como temos várias Camadas H264, temos que usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` `{Bitrate}` ou macro, o exemplo mostra o primeiro.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, criamos uma **Transformação** baseada na predefinição personalizada que definimos anteriormente. Ao criar uma Transformação, você deve primeiro usar [Get](https://docs.microsoft.com/rest/api/media/transforms/get) para verificar se um já existe. Se a Transformação existe, reutilize-a. 

Na coleção do Carteiro que você baixou, selecione **Transformações e Empregos**->**Criar ou Atualizar Transformar**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecione a guia **Corpo** e substitua o corpo pelo código json [definido anteriormente](#define-a-custom-preset). Para que os Serviços de Mídia apliquem o Transform ao vídeo ou áudio especificado, você precisa enviar um Trabalho essa Transformação.

Selecione **Enviar**. 

Para que os Serviços de Mídia apliquem o Transform ao vídeo ou áudio especificado, você precisa enviar um Trabalho sob essa Transformação. Para um exemplo completo que mostra como enviar um trabalho uma transformação, consulte [Tutorial: Stream arquivos de vídeo - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Próximas etapas

Ver [outras operações REST](https://docs.microsoft.com/rest/api/media/)
