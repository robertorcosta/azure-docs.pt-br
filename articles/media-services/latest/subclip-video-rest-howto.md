---
title: Subclipe um vídeo ao codificar com o Azure Media Services REST
description: Este tópico descreve como subclipar um vídeo ao codificar com o Azure Media Services usando REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514316"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclipe um vídeo ao codificar com serviços de mídia - REST

Você pode cortar ou subclipar um vídeo ao codificar usando um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Essa funcionalidade funciona com qualquer [Transformação](https://docs.microsoft.com/rest/api/media/transforms) que seja construída usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

O exemplo REST neste tópico cria um trabalho que apara um vídeo à medida que envia um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas descritas neste tópico, você precisa:

- [Crie uma conta do Azure Media Services](create-account-cli-how-to.md).
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Crie uma Transformação e um Ativo de saída. Você pode ver como criar um Transform e uma saída De ativos no [Encode um arquivo remoto com base na URL e transmitir o](stream-files-tutorial-with-rest.md) tutorial de vídeo - REST.
- Revise o tópico [conceito de codificação.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Crie um trabalho de subclipping

1. Na coleção carteiro que você baixou, selecione **Transformações e empregos** -> **Crie trabalho com Sub Clipping**.
    
    O pedido **PUT** se parece com isso:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável de ambiente "transformName" com seu nome de transformação. 
1. Selecione a guia **Corpo** e atualize o "myOutputAsset" com o nome do ativo de saída.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Pressione **Enviar**.

    Você vê a **Resposta** com as informações sobre o trabalho que foi criado e submetido e o status do trabalho. 

## <a name="next-steps"></a>Próximas etapas

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
