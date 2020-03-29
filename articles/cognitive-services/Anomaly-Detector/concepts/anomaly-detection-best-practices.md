---
title: Melhores práticas ao usar a API do Detector de Anomalias
titleSuffix: Azure Cognitive Services
description: Conheça as melhores práticas ao detectar anomalias com a API do Detector de Anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721628"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Melhores práticas para usar a API do Detector de Anomalias

A API do Detector de Anomalias é um serviço de detecção de anomalias apátridas. A precisão e o desempenho de seus resultados podem ser impactados por:

* Como os dados da sua série temporal são preparados.
* Os parâmetros de API do Detector de Anomalias que foram utilizados.
* O número de pontos de dados em sua solicitação de API. 

Use este artigo para aprender sobre as melhores práticas para usar a API obtendo os melhores resultados para seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando usar a detecção de anomalia de ponto em lote (inteiro) ou último (último)

O ponto final de detecção em lote da API do Detector de Anomalias permite detectar anomalias através de todos os dados da série Times. Neste modo de detecção, um único modelo estatístico é criado e aplicado a cada ponto do conjunto de dados. Se a sua série de tempo tiver as características abaixo, recomendamos usar a detecção em lote para visualizar seus dados em uma chamada de API.

* Uma série de tempo sazonal, com anomalias ocasionais.
* Uma série plana de tempo de tendência, com picos/mergulhos ocasionais. 

Não recomendamos o uso de detecção de anomalias em lote para monitoramento de dados em tempo real ou usá-los em séries temporâneas que não tenham características acima. 

* A detecção em lote cria e aplica apenas um modelo, a detecção para cada ponto é feita no contexto de séries inteiras. Se as tendências de dados da série temporal para cima e para baixo sem sazonalidade, alguns pontos de mudança (mergulhos e picos nos dados) podem ser perdidos pelo modelo. Da mesma forma, alguns pontos de mudança que são menos significativos do que os posteriores no conjunto de dados podem não ser contados como significativos o suficiente para serem incorporados ao modelo.

* A detecção em lote é mais lenta do que detectar o status de anomalia do ponto mais recente ao fazer o monitoramento de dados em tempo real, devido ao número de pontos analisados.

Para monitoramento de dados em tempo real, recomendamos detectar o status da anomalia apenas do seu último ponto de dados. Aplicando continuamente a detecção de pontos mais recente, o monitoramento de dados de streaming pode ser feito de forma mais eficiente e precisa.

O exemplo abaixo descreve o impacto que esses modos de detecção podem ter no desempenho. A primeira imagem mostra o resultado da detecção contínua do ponto mais recente do estado da anomalia ao longo de 28 pontos de dados vistos anteriormente. Os pontos vermelhos são anomalias.

![Uma imagem mostrando detecção de anomalias usando o ponto mais recente](../media/last.png)

Abaixo está o mesmo conjunto de dados usando detecção de anomalias em lote. O modelo construído para a operação ignorou várias anomalias, marcadas por retângulos.

![Uma imagem mostrando detecção de anomalias usando o método de lote](../media/entire.png)

## <a name="data-preparation"></a>Preparação dos dados

A API do Detector de Anomalias aceita dados de séries tempoforcadas formatados em um objeto de solicitação JSON. Uma série temporal pode ser qualquer dado numérico registrado ao longo do tempo em ordem seqüencial. Você pode enviar janelas de dados de suas séries tempométricas para o ponto final da API do Detector de Anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados que você pode enviar é de 12, e o máximo é de 8640 pontos. [Granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) é definida como a taxa em que seus dados são amostrados. 

Os pontos de dados enviados para a API do Detector de Anomalias devem ter um carimbo de tempo universal coordenado válido (UTC) e valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Se seus dados forem amostrados em um intervalo de `customInterval` tempo não padrão, você pode especiá-los adicionando o atributo em sua solicitação. Por exemplo, se a sua série for amostrada a cada 5 minutos, você poderá adicionar o seguinte à sua solicitação JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Pontos de dados perdidos

Os pontos de dados perdidos são comuns em conjuntos de dados de séries tempontos distribuídos uniformemente, especialmente aqueles com uma granularidade fina (um pequeno intervalo de amostragem). Por exemplo, dados amostrados a cada poucos minutos). Perder menos de 10% do número esperado de pontos em seus dados não deve ter um impacto negativo nos resultados de detecção. Considere preencher lacunas em seus dados com base em suas características, como substituir pontos de dados de um período anterior, interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Dados distribuídos agregados

A API do Detector de Anomalias funciona melhor em uma série temporal distribuída uniformemente. Se seus dados forem distribuídos aleatoriamente, você deve agregá-los por uma unidade de tempo, como por minuto, por hora ou diariamente, por exemplo.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detecção de anomalias em dados com padrões sazonais

Se você sabe que seus dados de séries tempois têm um padrão sazonal (que ocorre em intervalos regulares), você pode melhorar a precisão e o tempo de resposta da API. 

Especificar `period` um quando você constrói sua solicitação JSON pode reduzir a latência de detecção de anomalias em até 50%. O `period` é um inteiro que especifica aproximadamente quantos pontos de dados a série de tempo leva para repetir um padrão. Por exemplo, uma série temporal com um `period` ponto `7`de dados por dia teria um as , e uma `period` `7*24`série temporal com um ponto por hora (com o mesmo padrão semanal) teria um de . Se você não tem certeza dos padrões de seus dados, você não precisa especificar este parâmetro.

Para obter melhores `period`resultados, forneça 4 's de ponto de dados, mais um adicional. Por exemplo, dados por hora com um padrão semanal como descrito acima`7 * 24 * 4 + 1`devem fornecer 673 pontos de dados no órgão de solicitação ( ).

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitoramento em tempo real

Se os dados de streaming forem amostrados em um curto intervalo (por exemplo, segundos ou minutos), o envio do número recomendado de pontos de dados pode exceder o número máximo permitido da API do Detector de Anomalias (8640 pontos de dados). Se seus dados mostrarem um padrão sazonal estável, considere enviar uma amostra de seus dados da série temporal em um intervalo de tempo maior, como horas. A amostragem de seus dados dessa forma também pode melhorar visivelmente o tempo de resposta da API. 

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de Anomalias?](../overview.md)
* [Quickstart: Detecte anomalias em seus dados da série temporal usando a API Do Detector de Anomalias REST](../quickstarts/detect-data-anomalies-csharp.md)
