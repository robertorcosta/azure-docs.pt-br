---
title: Como usar a API do Detector de Anomalias em seus dados da série temporal
titleSuffix: Azure Cognitive Services
description: Saiba como detectar anomalias em seus dados, seja em lote ou em streaming de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840219"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Como: Use a API do Detector de Anomalias nos dados da sua série temporal  

A [API do Detector de Anomalias](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornece dois métodos de detecção de anomalias. Você pode detectar anomalias como um lote ao longo de suas séries de tempos, ou como seus dados são gerados detectando o status de anomalia do ponto de dados mais recente. O modelo de detecção retorna os resultados da anomalia juntamente com o valor esperado de cada ponto de dados e os limites de detecção de anomalias superiores e inferiores. você pode usar esses valores para visualizar a gama de valores normais e anomalias nos dados.

## <a name="anomaly-detection-modes"></a>Modos de detecção de anomalias 

A API do Detector de Anomalias fornece modos de detecção: lote e streaming.

> [!NOTE]
> A seguinte solicitação de URLs deve ser combinada com o ponto final apropriado para sua assinatura. Por exemplo: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Detecção em lote

Para detectar anomalias em um lote de pontos de dados em um determinado intervalo de tempo, use o URI de solicitação a seguir com os dados da série temporal: 

`/timeseries/entire/detect`. 

Ao enviar seus dados da série temporal de uma só vez, a API gerará um modelo usando toda a série e analisará cada ponto de dados com ele.  

### <a name="streaming-detection"></a>Detecção de streaming

Para detectar continuamente anomalias nos dados de streaming, use o URI de solicitação a seguir com seu último ponto de dados: 

`/timeseries/last/detect'`. 

Ao enviar novos pontos de dados à medida que você os gera, você pode monitorar seus dados em tempo real. Um modelo será gerado com os pontos de dados enviados, e a API determinará se o último ponto da série temporal é uma anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Ajustando os limites de detecção de anomalias inferiores e superiores

Por padrão, os limites superiore inferior para detecção `upperMargin`de `lowerMargin`anomalias são calculados usando `expectedValue`, e . Se você precisar de limites diferentes, `upperMargin` `lowerMargin`recomendamos aplicar a `marginScale` ou . Os limites seriam calculados da seguinte forma:

|Limite  |Cálculo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Os exemplos a seguir mostram um resultado de API do Detector de Anomalias em diferentes sensibilidades.

### <a name="example-with-sensitivity-at-99"></a>Exemplo com sensibilidade aos 99

![Sensibilidade Padrão](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemplo com sensibilidade aos 95

![Sensibilidade 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemplo com sensibilidade aos 85

![Sensibilidade 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de Anomalias?](../overview.md)
* [Quickstart: Detecte anomalias em seus dados da série temporal usando a API Do Detector de Anomalias REST](../quickstarts/detect-data-anomalies-csharp.md)
