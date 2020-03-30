---
title: Cenas de indexador de vídeo, fotos e quadros-chave
titleSuffix: Azure Media Services
description: Este tópico fornece uma visão geral das cenas, fotos e quadros do Indexador de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245935"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e quadros-chave

O Video Indexer suporta segmentar vídeos em unidades temporais com base em propriedades estruturais e semânticas. Esse recurso permite que os clientes naveguem, gerenciem e editem facilmente seu conteúdo de vídeo com base em granularidades variadas. Por exemplo, com base em cenas, tiros e quadros-chave, descritos neste tópico.   

![Cenas, capturas e quadros-chave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Detecção de cena  
 
O Video Indexer determina quando uma cena muda no vídeo com base em sinais visuais. Uma cena retrata um único evento e é composta de uma série de tomadas consecutivas, que estão semanticamente relacionadas. Uma miniatura de cena é o primeiro quadro-chave de seu tiro subjacente. O indexador de vídeo segmenta um vídeo em cenas baseadas na coerência de cores em tomadas consecutivas e recupera o tempo de início e fim de cada cena. A detecção de cena é considerada uma tarefa desafiadora, pois envolve quantificar aspectos semânticos de vídeos.

> [!NOTE]
> Aplicável a vídeos que contenham pelo menos 3 cenas.

## <a name="shot-detection"></a>Detecção de captura

O Video Indexer determina quando uma imagem muda no vídeo com base em pistas visuais, rastreando transições abruptas e graduais no esquema de cores de quadros adjacentes. Os metadados do tiro incluem um tempo de início e fim, bem como a lista de quadros-chave incluídos nessa tomada. As fotos são quadros consecutivos tirados da mesma câmera ao mesmo tempo.

## <a name="keyframe-detection"></a>Detecção de frame-chave

O Video Indexer seleciona os quadros que melhor representam cada tiro. Quadros-chave são os quadros representativos selecionados de todo o vídeo com base em propriedades estéticas (por exemplo, contraste e estabilidade). O Video Indexer recupera uma lista de IDs de quadro-chave como parte dos metadados da tomada, com base no qual os clientes podem extrair o quadro-chave como uma imagem de alta resolução.  

### <a name="extracting-keyframes"></a>Extraindo quadros-chave

Para extrair quadros-chave de alta resolução para o seu vídeo, você deve primeiro carregar e indexar o vídeo.

![Quadros chave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Com o site do Indexador de Vídeo

Para extrair quadros-chave usando o site do Indexador de Vídeo, carregue e indexe seu vídeo. Uma vez que o trabalho de indexação esteja concluído, clique no botão **Baixar** e selecione **Artefatos (ZIP)**. Isso baixará a pasta de artefatos para o seu computador. 

![Quadros chave](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Descompacte e abra a pasta. Na *pasta _KeyframeThumbnail,* e você encontrará todos os quadros-chave que foram extraídos do seu vídeo. 

#### <a name="with-the-video-indexer-api"></a>Com a API do indexador de vídeo

Para obter quadros-chave usando a API do Indexador de vídeo, carregue e indexe seu vídeo usando a chamada [Upload Video.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Uma vez que o trabalho de indexação esteja concluído, ligue [para obter índice de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Isso lhe dará todos os insights que o Video Indexer extratou do seu conteúdo em um arquivo JSON.  

Você receberá uma lista de IDs de quadro-chave como parte dos metadados de cada tiro. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Agora você precisará executar cada um desses IDs de quadro-chave na chamada [Obter miniaturas.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Isso baixará cada uma das imagens do quadro-chave para o seu computador. 

## <a name="editorial-shot-type-detection"></a>Detecção do tipo de tiro editorial

Os quadros-chave estão associados a tiros na saída JSON. 

O tipo de tiro associado a um tiro individual nos insights JSON representa seu tipo editorial. Você pode achar essas características de tipo de tiro úteis ao editar vídeos em clipes, trailers ou ao procurar um estilo específico de quadro-chave para fins artísticos. Os diferentes tipos são determinados com base na análise do primeiro quadro-chave de cada tiro. Os tiros são identificados pela escala, tamanho e localização dos rostos que aparecem em seu primeiro quadro-chave. 

O tamanho e a escala da filmagem são determinados com base na distância entre a câmera e os rostos que aparecem no quadro. Usando essas propriedades, o Video Indexer detecta os seguintes tipos de tiro:

* Amplo: mostra o corpo de uma pessoa inteira.
* Médio: mostra a parte superior do corpo e o rosto de uma pessoa.
* Close-up: mostra principalmente o rosto de uma pessoa.
* Close-up extremo: mostra o rosto de uma pessoa enchendo a tela. 

Os tipos de tiro também podem ser determinados pela localização dos caracteres sujeitos em relação ao centro do quadro. Esta propriedade define os seguintes tipos de tiro no Indexador de vídeo:

* Rosto esquerdo: uma pessoa aparece no lado esquerdo da moldura.
* Cara central: uma pessoa aparece na região central do quadro.
* Cara direita: uma pessoa aparece no lado direito da moldura.
* Ao ar livre: uma pessoa aparece em um ambiente ao ar livre.
* Interior: uma pessoa aparece em um ambiente interno.

Características adicionais:

* Dois tiros: mostra o rosto de duas pessoas de tamanho médio.
* Várias faces: mais de duas pessoas.


## <a name="next-steps"></a>Próximas etapas

[Examine a saída do indexador de vídeo produzido pela API](video-indexer-output-json-v2.md#scenes)
