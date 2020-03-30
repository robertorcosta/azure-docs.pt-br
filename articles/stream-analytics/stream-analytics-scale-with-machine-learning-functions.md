---
title: Dimensionar funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que usam funções de Machine Learning, configurando as unidades de particionamento e transmissão.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066997"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Dimensione seu trabalho de Stream Analytics com funções do Azure Machine Learning Studio (clássico)

> [!TIP]
> É altamente recomendável usar [UDFs de aprendizado de máquina do Azure](machine-learning-udf.md) em vez do Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a confiabilidade.

Este artigo discute como dimensionar eficientemente os trabalhos do Azure Stream Analytics que usam funções de Machine Learning do Azure. Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?

Uma função do Machine Learning no Stream Analytics pode ser usada como uma chamada de função normal na linguagem de consulta do Stream Analytics. Nos bastidores, no entanto, essas chamadas de função são na verdade solicitações do Azure Machine Learning Web Service.

Você pode melhorar o throughput de solicitações de serviço web de Aprendizado de Máquina ao "lotear" várias linhas juntas na mesma chamada de API de serviço web. Este agrupamento é chamado de mini-lote. Para obter mais informações, consulte [Azure Machine Learning Studio (clássico) Web Services](../machine-learning/studio/consume-web-services.md). O suporte para o Azure Machine Learning Studio (clássico) no Stream Analytics está em pré-visualização.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar seu trabalho do Stream Analytics com funções de Machine Learning

Existem dois parâmetros para configurar a função Machine Learning usada pelo seu trabalho de Stream Analytics:

* Tamanho do lote das chamadas da função Machine Learning.
* O número de Unidades de Streaming (SUs) provisionadas para o trabalho do Stream Analytics.

Para determinar os valores apropriados para sus, decida se você gostaria de otimizar a latência do trabalho do Stream Analytics ou o throughput de cada SU. Os SUs podem sempre ser adicionados a um trabalho para aumentar o throughput de uma consulta de Stream Analytics bem particionada. SUs adicionais aumentam o custo de execução do trabalho.

Determine a *tolerância* de latência para o seu trabalho de Stream Analytics. Aumentar o tamanho do lote aumentará a latência de suas solicitações de Machine Learning do Azure e a latência do trabalho do Stream Analytics.

Aumentar o tamanho do lote permite que o trabalho do Stream Analytics processe **mais eventos** com o **mesmo número** de solicitações de serviçoweb de Aprendizado de Máquina. O aumento da latência do serviço web de Machine Learning é geralmente sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico para um serviço web de Machine Learning em qualquer situação. O tamanho do lote padrão para solicitações de serviço web é de 1000. Você pode alterar esse tamanho padrão usando a [API Stream Analytics REST](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir o tamanho do lote, você pode definir o número de unidades de streaming (SUs), com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

A cada 6 SUs, há 20 conexões simultâneas com o serviço web Machine Learning. No entanto, 1 emprego nos EUA e 3 empregos na UA recebem 20 conexões simultâneas.  

Se o seu aplicativo gera 200.000 eventos por segundo, e o tamanho do lote é de 1000, então a latência resultante do serviço web é de 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações ao serviço web de Aprendizado de Máquina a cada segundo. Com 20 conexões, o trabalho do Stream Analytics pode processar 20.000 eventos em 200 ms e 100.000 eventos em um segundo.

Para processar 200.000 eventos por segundo, o trabalho do Stream Analytics precisa de 40 conexões simultâneas, que saem para 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho do Stream Analytics para o ponto de extremidade de serviço Web do Machine Learning – a cada 6 SUs há, no máximo, 20 conexões simultâneas com o serviço Web do Machine Learning.

![Scale Stream Analytics com Machine Learning Functions dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scale Stream Analytics com Machine Learning Functions dois exemplos de trabalho")

Em geral, sendo ***B*** o tamanho do lote e ***L*** a latência do serviço Web com o tamanho do lote B em milissegundos, a produtividade de um trabalho do Stream Analytics com ***N*** SUs será:

![Fórmula para escalar o Stream Analytics com funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula para escalar o Stream Analytics com funções de Machine Learning")

Você também pode configurar as 'chamadas simultâneas máximas' no serviço web Machine Learning. Recomenda-se definir este parâmetro para o valor máximo (200 atualmente).

Para saber mais sobre essa configuração, confira o [artigo sobre dimensionamento de serviços Web do Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – análise de sentimento
O exemplo a seguir inclui um trabalho do Stream Analytics com a função do Machine Learning de análise de sentimento, conforme descrito no [Tutorial de integração do Machine Learning do Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta simples e totalmente particionada, seguida pela função **sentimento**, conforme mostrado abaixo:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho de Stream Analytics, que faz análise de sentimento de tweets a uma taxa de 10.000 tweets por segundo.

Usando 1 SU, esse trabalho do Stream Analytics poderia lidar com o tráfego? O trabalho pode acompanhar a entrada usando o tamanho do lote padrão de 1000. A latência padrão do serviço web machine learning de análise de sentimento (com um tamanho de lote padrão de 1000) não cria mais do que um segundo de latência.

A latência **geral** ou de ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse trabalho do Stream Analytics, *especialmente* nas chamadas de função do Machine Learning. Com um tamanho de lote de 1000, um throughput de 10.000 eventos leva cerca de 10 solicitações para o serviço web. Mesmo com uma AU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, o trabalho do Stream Analytics precisará processar 1.000.000 de tweets por segundo. Há duas opções para realizar o aumento da escala:

1. Aumente o tamanho do lote.
2. Particione o fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você terá que prover mais SUs para ter mais solicitações simultâneas de serviços web de Machine Learning. Esse maior número de SUs, aumenta o **custo**do trabalho.

Vejamos o dimensionamento usando as seguintes medidas de latência para cada tamanho de lote:

| Latency | Tamanho do lote |
| --- | --- |
| 200 ms | Lotes de 1000 eventos ou abaixo |
| 250 ms | Lotes de 5.000 eventos |
| 300 ms | Lotes de 10.000 eventos |
| 500 ms | Lotes de 25.000 eventos |

1. Usando a primeira opção (**não** Provisionando mais SUs). O tamanho do lote pode ser aumentado para **25.000**. Aumentar o tamanho do lote dessa forma permitirá que o trabalho processe 1.000.000 eventos com 20 conexões simultâneas ao serviço web machine learning (com uma latência de 500 ms por chamada). Portanto, a latência adicional do trabalho do Stream Analytics causada pelas solicitações de função de sentimento aumentaria de **200 ms** para **500 ms** em comparação com as solicitações de serviço Web do Machine Learning. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, pois os serviços web de Aprendizado de Máquina exigem que o tamanho da carga útil de uma solicitação seja de 4 MB ou menor, e o tempo limite de solicitações de serviço web após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200 ms, cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2.500 |5.000 |20,000 |30,000 |50.000 |
| **3 SUs** |2.500 |5.000 |20,000 |30,000 |50.000 |
| **6 SUs** |2.500 |5.000 |20,000 |30,000 |50.000 |
| **12 SUs** |5.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7.500 |15,000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20,000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Agora, você já deve ter uma boa compreensão de como as funções do Machine Learning funcionam no Stream Analytics. É provável que você também entenda que os jobs do Stream Analytics "extraem" dados de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de obtenção afeta as solicitações de serviço Web do Machine Learning?

Normalmente, o tamanho do lote que definimos para as funções de Machine Learning não será exatamente divisível pelo número de eventos retornados por cada trabalho do Stream Analytics "puxar". Quando isso ocorre, o serviço Web do Machine Learning é chamado com lotes "parciais". O uso de lotes parciais evita incorrer em sobrecarga suplementar de latência de trabalho em eventos de coalizão de puxar para puxar.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. São **SOLICITAÇÕES DE FUNÇÃO,** **EVENTOS DE FUNÇÃO** E **SOLICITAÇÕES DE FUNÇÃO FALHA,** conforme mostrado no gráfico abaixo.

![Escalar o Stream Analytics com métricas de funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Escalar o Stream Analytics com métricas de funções de Machine Learning")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: o número de solicitações de função.

**EVENTOS DE FUNÇÃO**: o número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: o número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações

Para escalar um trabalho de Stream Analytics com funções de Machine Learning, considere os seguintes fatores:

1. A taxa de evento de entrada.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote das solicitações de serviço web machine learning).
3. Os SUs de Stream Analytics provisionados e o número de solicitações de serviçoweb de Aprendizado de Máquina (os custos adicionais relacionados à função).

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, o [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Stream Analytics, confira:

* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
