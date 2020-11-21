---
title: Operações de análise espacial
titleSuffix: Azure Cognitive Services
description: As operações de análise espacial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014536"
---
# <a name="spatial-analysis-operations"></a>Operações de análise espacial

A análise espacial permite a análise do vídeo de streaming em tempo real de dispositivos com câmera. Para cada dispositivo com câmera configurado, as operações para análise espacial gerarão um fluxo de saída de mensagens JSON enviadas à sua instância do Hub IoT do Azure. 

O contêiner análise espacial implementa as seguintes operações:

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount | Conta as pessoas em uma zona designada no campo de exibição da câmera. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br> Emite um evento _personLineEvent_ quando a pessoa cruza a zona e fornece informações direcionais. |
| cognitivaservices. Vision. spatialanalysis-persondistance | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

Todas as operações acima também estão disponíveis na `.debug` versão, que têm a capacidade de visualizar os quadros de vídeo conforme eles estão sendo processados. Você precisará executar o `xhost +` no computador host para habilitar a visualização de quadros de vídeo e eventos.

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount. Debug | Conta as pessoas em uma zona designada no campo de exibição da câmera. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline. Debug | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon. Debug | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br> Emite um evento _personLineEvent_ quando a pessoa cruza a zona e fornece informações direcionais. |
| cognitivaservices. Vision. spatialanalysis-persondistance. Debug | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

A análise espacial também pode ser executada com a [análise de vídeo ao vivo](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) como seu módulo de ia de vídeo. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificador da operação| Description|
|---------|---------|
| cognitivaservices. Vision. spatialanalysis-personcount. livevideoanalytics | Conta as pessoas em uma zona designada no campo de exibição da câmera. <br> Emite um evento _personCountEvent_ inicial e, em seguida, _personCountEvent_ eventos quando a contagem é alterada.  |
| cognitivaservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitivaservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Controla quando uma pessoa cruza uma linha designada no campo de exibição da câmera. <br> Emite um evento _personLineEvent_ quando a pessoa cruza a zona e fornece informações direcionais. |
| cognitivaservices. Vision. spatialanalysis-persondistance. livevideoanalytics | Rastreia quando as pessoas violam uma regra de distância. <br> Emite um _personDistanceEvent_ periodicamente com o local de cada violação de distância. |

As operações de análise de vídeo ao vivo também estão disponíveis na `.debug` versão (por exemplo, cognitivaservices. Vision. spatialanalysis-personcount. livevideoanalytics. Debug) que tem a capacidade de visualizar os quadros de vídeo como sendo processados. Você precisará executar `xhost +` no computador host para habilitar a visualização dos quadros e eventos de vídeo

> [!IMPORTANT]
> Os modelos de ia da pesquisa Visual computacional detectam e localizam a presença humana na seqüência de vídeo e na saída usando uma caixa delimitadora em um corpo humano. Os modelos de ia não tentam detectar rostos ou descobrir as identidades ou os dados demográficos de indivíduos.

Esses são os parâmetros necessários para cada uma dessas operações de análise espacial.

| Parâmetros de operação| Description|
|---------|---------|
| ID da Operação | O identificador da operação da tabela acima.|
| Habilitado | Booliano: verdadeiro ou falso|
| VIDEO_URL| A URL RTSP para o dispositivo de câmera (exemplo: `rtsp://username:password@url` ). A análise espacial dá suporte ao fluxo codificado H. 264 por meio de RTSP, http ou MP4 |
| VIDEO_SOURCE_ID | Um nome amigável para o dispositivo de câmera ou fluxo de vídeo. Isso será retornado com a saída do evento JSON.|
| VIDEO_IS_LIVE| Verdadeiro para dispositivos de câmera; false para vídeos gravados.|
| VIDEO_DECODE_GPU_INDEX| Qual GPU decodificará o quadro de vídeo. Por padrão, é 0. Deve ser o mesmo que o `gpu_index` em outras configurações de nó como `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON que indica em qual GPU executar o nó do detector. Deve estar no seguinte formato: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configuração de JSON para a zona e a linha, conforme descrito abaixo.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-personcount

 Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de um polígono. O polígono representa as áreas nas quais as pessoas são controladas ou contadas e os pontos do polígono são baseados em coordenadas normalizadas (0-1), em que o canto superior esquerdo é (0,0, 0,0) e o canto inferior direito é (1,0, 1,0).   
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcount** deve ser `count` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem `interval` é alterada ou para enviar eventos periodicamente, independentemente de a contagem ter sido alterada ou não.
| `interval` | string| Um tempo em segundos que a contagem de pessoas será agregada antes de um evento ser acionado. A operação continuará a analisar a cena com uma taxa constante e retornará a contagem mais comum sobre esse intervalo. O intervalo de agregação é aplicável ao `event` e ao `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configuração de linha para cognitivaservices. Vision. spatialanalysis-personcrossingline

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma linha. Você pode configurar várias linhas de cruzamento para esta operação.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `lines` | list| Lista de linhas.|
| `name` | string| Nome amigável para esta linha.|
| `line` | list| A definição da linha. Essa é uma linha direcional que permite que você entenda "entry" vs. "Exit".|
| `start` | par de valor| coordenadas x, y para o ponto inicial da linha. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. |
| `end` | par de valor| coordenadas x, y para o ponto final da linha. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. |
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcrossingline** deve ser `linecrossing` .|
|`trigger`|string|O tipo de gatilho para enviar um evento.<br>Valores com suporte: "Event": Fire quando alguém cruza a linha.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-personcrossingpolygon

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Você pode configurar várias zonas para esta operação.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de polígono. O polígono representa as áreas nas quais as pessoas são rastreadas ou contadas. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-personcrossingpolygon,** isso deve ser `enter` ou `exit` .|
| `trigger`|string|O tipo de gatilho para enviar um evento<br>Valores com suporte: "Event": Fire quando alguém entra ou sai da zona.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configuração de zona para cognitivaservices. Vision. spatialanalysis-persondistance

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona para **cognitivaservices. Vision. spatialanalysis-persondistance**. Você pode configurar várias zonas para esta operação.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `zones` | list| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | list| Cada par de valor representa o x, y para vértices de polígono. O polígono representa as áreas nas quais as pessoas são contadas e a distância entre as pessoas é medida. Os valores float representam a posição do vértice em relação ao canto superior, esquerdo. Para calcular os valores x, y absolutos, você multiplica esses valores pelo tamanho do quadro. 
| `threshold` | FLOAT| Os eventos são egressos quando a confiança dos modelos de ia é maior ou igual ao valor. |
| `type` | string| Para **cognitivaservices. Vision. spatialanalysis-persondistance** deve ser `people_distance` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores com suporte são `event` para enviar eventos quando a contagem `interval` é alterada ou para enviar eventos periodicamente, independentemente de a contagem ter sido alterada ou não.
| `interval` | string | Um tempo em segundos que as violações serão agregadas antes de um evento ser acionado. O intervalo de agregação é aplicável ao `event` e ao `interval` .|
| `output_frequency` | INT | A taxa em que os eventos são reegressos. Quando `output_frequency` = x, cada evento X é egresso, por exemplo, `output_frequency` = 2 significa que todos os outros eventos são gerados. O output_frequency é aplicável ao `event` e ao `interval` .|
| `minimum_distance_threshold` | FLOAT| Uma distância em pés que disparará um evento "TooClose" quando as pessoas forem menores do que essa distância.|
| `maximum_distance_threshold` | FLOAT| Uma distância em pés que irá disparar um evento "TooFar" quando as pessoas forem maiores que essa distância.|

Este é um exemplo de uma entrada JSON para o parâmetro DETECTOR_NODE_CONFIG que configura uma zona **cognitivaservices. Vision. spatialanalysis-persondistance** .

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `gpu_index` | string| O índice de GPU no qual esta operação será executada.|
| `do_calibration` | string | Indica que a calibragem está ativada. `do_calibration` deve ser verdadeiro para **cognitivaservices. Vision. spatialanalysis-persondistance** para funcionar corretamente.|

Consulte as diretrizes de [posicionamento da câmera](spatial-analysis-camera-placement.md)  para saber mais sobre as configurações de zona e linha.

## <a name="spatial-analysis-operation-output"></a>Saída da operação de análise espacial

Os eventos de cada operação são saída para o Hub IoT do Azure no formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcount ia insights

Exemplo de JSON para uma saída de evento por esta operação.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo de evento | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| ' Enter ' ou ' Exit '|
| `side` | INT| O número do lado do polígono que a pessoa cruzou|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi cruzada|
| `trigger` | string| O tipo de gatilho é ' Event ' ou ' interval ' dependendo do valor de `trigger` no SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | Indica se a calibragem da câmera para o plano de chão está "completa"|
| `cameraHeight` | FLOAT | A altura da câmera acima do início em pés. Isso é inferido da calibragem automática. |
| `focalLength` | FLOAT | O comprimento focal da câmera em pixels. Isso é inferido da calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera da vertical. Isso é inferido da calibragem automática.|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcrossingline ia insights

Exemplo de JSON para a saída de detecções por esta operação.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome do campo de evento | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção de cruzamentos de linha, ' CrossLeft ' ou ' CrossRight '|
| `zone` | string | O campo "nome" da linha que foi cruzada|

| Nome do campo de detecções | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|


> [!IMPORTANT]
> O modelo de ia detecta uma pessoa independentemente de se a pessoa está voltada para longe ou afastada da câmera. O modelo de ia não executa a detecção de face ou o reconhecimento e não emite nenhuma informação biométrica. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-personcrossingpolygon ia insights

Exemplo de JSON para a saída de detecções por esta operação.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo de evento | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `trackinId` | string| Identificador exclusivo da pessoa detectada|
| `status` | string| Direção de cruzamentos de polígono, ' Enter ' ou ' Exit '|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi cruzada|

| Nome do campo de detecções | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON para cognitivaservices. Vision. spatialanalysis-persondistance ia insights

Exemplo de JSON para a saída de detecções por esta operação.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo de evento | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID do evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz de tamanho 1 do identificador exclusivo da detecção de pessoa que disparou este evento|
| `properties` | collection| Coleção de valores|
| `personCount` | INT| Número de pessoas detectadas quando o evento foi emitido|
| `averageDistance` | FLOAT| A distância média entre todas as pessoas detectadas em pés|
| `minimumDistanceThreshold` | FLOAT| A distância, em pés, que disparará um evento "TooClose" quando as pessoas forem menores do que essa distância.|
| `maximumDistanceThreshold` | FLOAT| A distância, em pés, que disparará um evento "TooFar" quando as pessoas forem maiores do que distância.|
| `eventName` | string| `TooClose`O nome do evento está `minimumDistanceThreshold` violado, `TooFar` quando `maximumDistanceThreshold` é violado ou `unknown` quando a calibragem automática não foi concluída|
| `distanceViolationPersonCount` | INT| Número de pessoas detectadas na violação `minimumDistanceThreshold` ou `maximumDistanceThreshold`|
| `zone` | string | O campo "Name" do polígono que representa a zona que foi monitorada para distancing entre as pessoas|
| `trigger` | string| O tipo de gatilho é ' Event ' ou ' interval ' dependendo do valor de `trigger` no SPACEANALYTICS_CONFIG|

| Nome do campo de detecções | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID de detecção|
| `type` | string| Tipo de detecção|
| `region` | collection| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | collection| Pontos superior esquerdo e inferior direito quando o tipo de região é RECTANGLE |
| `confidence` | FLOAT| Confiança do algoritmo|
| `centerGroundPoint` | 2 valores float| `x`, os `y` valores com as coordenadas do local inferido da pessoa no chão em pés. `x` é distância da câmera perpendicular ao plano de imagem da câmera projetado no chão em pés. `y` a distância da câmera é paralela ao plano de imagem projetado no chão em pés.|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | string| ID da Câmera|
| `timestamp` | date| Data UTC quando a carga JSON foi emitida|
| `width` | INT | Largura do quadro do vídeo|
| `height` | INT | Altura do quadro do vídeo|
| `frameId` | INT | Identificador de quadro|
| `cameraCallibrationInfo` | collection | Coleção de valores|
| `status` | string | Indica se a calibragem da câmera para o plano de chão está "completa"|
| `cameraHeight` | FLOAT | A altura da câmera acima do início em pés. Isso é inferido da calibragem automática. |
| `focalLength` | FLOAT | O comprimento focal da câmera em pixels. Isso é inferido da calibragem automática. |
| `tiltUpAngle` | FLOAT | O ângulo de inclinação da câmera da vertical. Isso é inferido da calibragem automática.|


## <a name="use-the-output-generated-by-the-container"></a>Usar a saída gerada pelo contêiner

Você pode desejar integrar a detecção de análise espacial ou eventos em seu aplicativo. Aqui estão algumas abordagens a serem consideradas: 

* Use o SDK do hub de eventos do Azure para a linguagem de programação escolhida para se conectar ao ponto de extremidade do Hub IoT do Azure e receber os eventos. Consulte [ler mensagens do dispositivo para a nuvem do ponto de extremidade interno](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
* Configure o **Roteamento de mensagens** no Hub IOT do Azure para enviar os eventos para outros pontos de extremidade ou salve os eventos no armazenamento de dados. Consulte [Roteamento de mensagens do Hub IOT](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 
* Configure um trabalho de Azure Stream Analytics para processar os eventos em tempo real à medida que eles chegam e criar visualizações. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Implantando operações de análise espacial em escala (várias câmeras)

Para obter o melhor desempenho e utilização das GPUs, você pode implantar qualquer operação de análise espacial em várias câmeras usando instâncias de grafo. Abaixo está um exemplo para executar a `cognitiveservices.vision.spatialanalysis-personcount` operação em cinco câmeras.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Nome | Tipo| Description|
|---------|---------|---------|
| `batch_size` | INT | Indica o número de câmeras que serão usadas na operação. |

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)