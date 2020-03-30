---
title: Detecção de anomalias no Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics e o Azure Machine Learning em conjunto para detectar anomalias.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525525"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

Disponível na nuvem e no Azure IoT Edge, o Azure Stream Analytics oferece recursos internos de detecção de anomalias baseados em aprendizado de máquina que podem ser usados para monitorar as duas anomalias que ocorrem com mais frequência: temporárias e persistentes. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint**, você pode detectar anomalias diretamente no seu trabalho do Stream Analytics.

Os modelos de machine learning supõem uma série temporal incluída na amostra de maneira uniforme. Se a série temporal não for uniforme, insira uma etapa de agregação com uma janela em cascata antes de chamar a detecção de anomalias.

As operações de aprendizado de máquina não suportam tendências de sazonalidade ou correlações multivariadas neste momento.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detecção de anomalias usando aprendizado de máquina no Azure Stream Analytics

O vídeo a seguir demonstra como detectar uma anomalia em tempo real usando funções de aprendizado de máquina no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Comportamento do modelo

De modo geral, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte de seu intervalo de valores normal para o período. O modelo considera o histórico de eventos ao longo da janela deslizante apenas para verificar se o evento atual é anormal. Conforme a janela deslizante se move, os valores antigos são removidos do treinamento do modelo.

As funções operam estabelecendo um determinado valor normal com base no que foi observado até então. As exceções são identificadas pela comparação em relação ao normal estabelecido, no nível de confiança. O tamanho da janela deve se basear nos eventos mínimos necessários para treinar o modelo para comportamento normal, de modo que ele esteja apto a reconhecê-la quando uma anomalia ocorrer.

O tempo de resposta do modelo aumenta com o tamanho da história porque precisa ser comparado com um número maior de eventos passados. É recomendável incluir apenas o número necessário de eventos para obter melhor desempenho.

As lacunas na série temporal podem ser um resultado do não recebimento de eventos pelo modelo em determinados pontos no tempo. Essa situação é tratada pelo Stream Analytics usando lógica de imputação. O tamanho do histórico e a duração para a mesma janela deslizante são usados para calcular a taxa média na qual os eventos são esperados.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um Hub iot com dados com diferentes padrões de anomalia. Um trabalho ASA pode ser configurado com essas funções de detecção de anomalias para ler a partir deste Hub Iot e detectar anomalias.

## <a name="spike-and-dip"></a>Pico e queda

As anomalias temporárias em um fluxo de eventos de série temporal são conhecidas como picos e quedas. Os picos e quedas podem ser monitorados usando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e queda](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a pontuação calculada para o menor pico provavelmente não será significativa o suficiente em comparação com a pontuação para o primeiro pico no nível de confiança especificado. Você pode tentar diminuir o nível de confiança do modelo para detectar tais anomalias. No entanto, se começar a receber muitos alertas, é possível usar um intervalo de confiança maior.

O exemplo de consulta a seguir pressupõe uma taxa uniforme de entrada de um evento por segundo em uma janela deslizante de 2 minutos com um histórico de 120 eventos. A instrução SELECT final extrai e gera a pontuação e o status da anomalia com um nível de confiança de 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Ponto de alteração

As anomalias persistentes em um fluxo de eventos de série temporal são alterações na distribuição de valores no fluxo de eventos, como alterações e tendências no nível. No Stream Analytics, tais anomalias são detectadas usando o operador [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) baseado em Machine Learning.

As alterações persistentes duram muito mais que picos e quedas e podem indicar eventos catastróficos. As alterações persistentes geralmente não são vistas a olho nu, mas podem ser detectadas com o operador **AnomalyDetection_ChangePoint**.

A seguinte imagem é um exemplo de alteração no nível:

![Exemplo de anomalia de alteração no nível](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A seguinte imagem é um exemplo de alteração na tendência:

![Exemplo de anomalia de alteração na tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

O exemplo de consulta a seguir pressupõe uma taxa uniforme de entrada de um evento por segundo em uma janela deslizante de 20 minutos com um tamanho de histórico de 1200 eventos. A instrução SELECT final extrai e gera a pontuação e o status da anomalia com um nível de confiança de 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Características de desempenho

O desempenho desses modelos depende do tamanho do histórico, duração da janela, carga do evento e se o particionamento do nível de função é usado. Esta seção discute essas configurações e fornece amostras de como sustentar taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

* **Tamanho da história** - Esses modelos funcionam linearmente com **o tamanho da história.** Quanto maior o tamanho da história, mais tempo os modelos demoram para marcar um novo evento. Isso porque os modelos comparam o novo evento com cada um dos eventos passados no buffer histórico.
* **Duração da janela** - A **duração da janela** deve refletir quanto tempo leva para receber tantos eventos quanto suscitado pelo tamanho do histórico. Sem tantos eventos na janela, o Azure Stream Analytics imputaria valores perdidos. Portanto, o consumo de CPU é uma função do tamanho do histórico.
* **Carga de evento** - Quanto maior a carga do **evento,** mais trabalho é realizado pelos modelos, o que impacta o consumo de CPU. O trabalho pode ser dimensionado tornando-o vergonhosamente paralelo, assumindo que faz sentido para a lógica empresarial usar mais partições de entrada.
* **Particionamento do nível de** - função O**particionamento do nível da função** é feito usando ```PARTITION BY``` a chamada da função de detecção de anomalias. Este tipo de particionamento adiciona uma sobrecarga, pois o estado precisa ser mantido para vários modelos ao mesmo tempo. Particionamento do nível de função é usado em cenários como particionamento de nível de dispositivo.

### <a name="relationship"></a>Relação
O tamanho do histórico, a duração da janela e a carga total do evento estão relacionados da seguinte maneira:

windowDuração (em ms) = 1000 * historySize / (Total Input Events Per Sec / Input Partition Count)

Ao particionar a função por dispositivoId, adicione "PARTITION BY deviceId" à chamada da função de detecção de anomalias.

### <a name="observations"></a>Observações
A tabela a seguir inclui as observações de throughput para um único nó (6 SU) para o caso não particionado:

| Tamanho da história (eventos) | Duração da janela (ms) | Total de eventos de entrada por segundo |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2.200 |
| 600 | 728 | 1,650 |
| 6.000 | 10,910 | 1.100 |

A tabela a seguir inclui as observações de throughput para um único nó (6 SU) para o caso particionado:

| Tamanho da história (eventos) | Duração da janela (ms) | Total de eventos de entrada por segundo | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1.100 | 10 |
| 600 | 10,910 | 1.100 | 10 |
| 6.000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6.000 | 2,181,819 | <550 | 100 |

O código de amostra para executar as configurações não partições acima está localizado no [repo Streaming At Scale](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) do Azure Samples. O código cria um trabalho de análise de fluxo sem particionamento de nível de função, que usa o Event Hub como entrada e saída. A carga de entrada é gerada usando clientes de teste. Cada evento de entrada é um documento json 1KB. Os eventos simulam um dispositivo IoT enviando dados JSON (para até dispositivos 1K). O tamanho do histórico, a duração da janela e a carga total do evento são variados em 2 partições de entrada.

> [!Note]
> Para uma estimativa mais precisa, personalize as amostras para se adequar em seu cenário.

### <a name="identifying-bottlenecks"></a>Identificação de gargalos
Use o painel Metrics em seu trabalho do Azure Stream Analytics para identificar gargalos em seu pipeline. Revisar **eventos de entrada/saída** para throughput e ["Watermark Delay"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **Backlogged Events** para ver se o trabalho está acompanhando a taxa de entrada. Para as métricas do Event Hub, procure **solicitações estranguladas** e ajuste as Unidades de Limiar de acordo. Para as métricas do Cosmos DB, revise **o alcance de tecla RU/s consumido por partição** em Throughput para garantir que suas faixas de tecla de partição sejam consumidas uniformemente. Para O Azure SQL DB, monitore **o Log IO** e **a CPU**.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

