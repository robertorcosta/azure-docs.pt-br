---
title: Encode custom transform usando Media Services v3 Azure CLI | Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando o Azure CLI.
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
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382946"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Como codificar com uma transformação personalizada - Azure CLI

Ao codificar com o Azure Media Services, você pode começar rapidamente com uma das predefinições incorporadas recomendadas, com base nas práticas recomendadas do setor, como demonstrado no quickstart [de arquivos streaming.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Você também pode construir uma predefinição personalizada para atingir seus requisitos específicos de cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores para altura e largura no conteúdo AVC devem ser um múltiplo de 4.
* No Azure Media Services v3, todas as bitrates de codificação estão em bits por segundo. Isto é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 foi especificada como 128 (kilobits/segundo), em v3 seria definida como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Defina uma predefinição personalizada

O exemplo a seguir define o corpo de solicitação de uma nova Transformação. Definimos um conjunto de saídas que queremos gerar quando este Transform for usado.

Neste exemplo, adicionamos primeiro uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos rótulos para que possam ser usados nos nomes dos arquivos de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas em 50% da resolução do vídeo de entrada, e em três carimbos de tempo - {25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, especificamos o formato para os arquivos de saída - um para vídeo + áudio e outro para as miniaturas. Como temos várias Camadas H264, temos que usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` `{Bitrate}` ou macro, o exemplo mostra o primeiro.

Vamos salvar essa transformação em um arquivo. Neste exemplo, nomeamos `customPreset.json`o arquivo .

```json
{
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
```

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, criamos uma **Transformação** baseada na predefinição personalizada que definimos anteriormente. Ao criar uma Transformação, você deve primeiro verificar se uma já existe. Se a Transformação existe, reutilize-a. O `show` comando a `customTransformName` seguir retorna a transformação se existir:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

O comando Azure CLI a seguir cria o Transform com base na predefinição personalizada (definida anteriormente).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Para que os Serviços de Mídia apliquem o Transform ao vídeo ou áudio especificado, você precisa enviar um Trabalho essa Transformação. Para um exemplo completo que mostra como enviar um trabalho sob uma transformação, consulte [Quickstart: Stream arquivos de vídeo - Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Confira também

[Azure CLI](/cli/azure/ams)
