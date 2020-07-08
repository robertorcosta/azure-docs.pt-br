---
title: Dimensionar funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que usam funções de Machine Learning, configurando as unidades de particionamento e transmissão.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: 1493a15a97ca88d0ed914f78b1906088c03dff10
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037402"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Dimensionar seu trabalho do Stream Analytics com funções do Azure Machine Learning Studio (clássico)

> [!TIP]
> É altamente recomendado usar [UDFs do Azure Machine Learning](machine-learning-udf.md) em vez de UDFs do Azure Machine Learning Studio (clássico) para melhorar o desempenho e a confiabilidade.

Este artigo aborda como dimensionar com eficiência os trabalhos do Azure Stream Analytics que usam funções do Azure Machine Learning. Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?

Uma função do Machine Learning no Stream Analytics pode ser usada como uma chamada de função normal na linguagem de consulta do Stream Analytics. No entanto, nos bastidores, essas chamadas de função são, na verdade, solicitações de Serviço Web do Azure Machine Learning.

Você pode melhorar a taxa de transferência das solicitações de serviço Web do Machine Learning por meio de "envio em lote" de várias linhas na mesma chamada à API do serviço Web. Esse agrupamento é chamado de minilote. Para saber mais, confira [Serviços Web do Machine Learning Studio (clássico)](../machine-learning/studio/consume-web-services.md). O suporte para o Azure Machine Learning Studio (clássico) no Stream Analytics está na versão prévia.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar seu trabalho do Stream Analytics com funções de Machine Learning

Há dois parâmetros para configurar a função do Machine Learning usada pelo seu trabalho do Stream Analytics:

* Tamanho do lote das chamadas de função do Machine Learning.
* O número de unidades de streaming (SUs) provisionadas para o trabalho do Stream Analytics.

Para determinar os valores apropriados para as SUs, decida se deseja otimizar a latência do trabalho do Stream Analytics ou a taxa de transferência de cada SU. As SUs podem ser sempre adicionadas a um trabalho a fim de aumentar a taxa de transferência de uma consulta do Stream Analytics bem particionada. SUs adicionais aumentam o custo da execução do trabalho.

Determine a *tolerância* de latência do seu trabalho do Stream Analytics. Aumentar o tamanho do lote aumentará a latência das solicitações do Azure Machine Learning e a latência do trabalho do Stream Analytics.

O aumento do tamanho do lote permite que o trabalho do Stream Analytics processe **mais eventos** com o **mesmo número** de solicitações de serviço Web do Machine Learning. Normalmente, o aumento da latência de serviço Web do Machine Learning é sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico para um serviço Web do Machine Learning em qualquer situação. O tamanho de lote padrão para solicitações de serviço Web é 1000. Você pode alterar esse tamanho padrão usando a [API REST do Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente do PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir o tamanho de lote, defina o número de unidades de streamings (SUs) com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SUs recebem 20 conexões simultâneas com o serviço Web do Machine Learning. No entanto, um trabalho de SU e 3 trabalhos de SU recebem 20 conexões simultâneas.  

Se o seu aplicativo gera 200.000 eventos por segundo, e o tamanho do lote é 1000, a latência do serviço Web resultante é de 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações para o serviço Web do Machine Learning a cada segundo. Com 20 conexões, o trabalho do Stream Analytics pode processar 20.000 eventos em 200 ms e 100.000 eventos em um segundo.

Para processar 200.000 eventos por segundo, o trabalho do Stream Analytics precisará de 40 conexões simultâneas, provenientes de 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho do Stream Analytics para o ponto de extremidade de serviço Web do Machine Learning – a cada 6 SUs há, no máximo, 20 conexões simultâneas com o serviço Web do Machine Learning.

![Escalar o Stream Analytics com funções de Machine Learning, dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Escalar o Stream Analytics com funções de Machine Learning, dois exemplos de trabalho")

Em geral, sendo ***B*** o tamanho do lote e ***L*** a latência do serviço Web com o tamanho do lote B em milissegundos, a produtividade de um trabalho do Stream Analytics com ***N*** SUs será:

![Fórmula para escalar o Stream Analytics com funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula para escalar o Stream Analytics com funções de Machine Learning")

Você também pode configurar o “máximo de chamadas simultâneas”' no serviço Web do Machine Learning. É recomendado definir esse parâmetro como o valor máximo (200 no momento).

Para saber mais sobre essa configuração, confira o [artigo sobre dimensionamento de serviços Web do Machine Learning](../machine-learning/studio/create-endpoint.md).

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

Vamos examinar a configuração necessária para criar um trabalho do Stream Analytics, que faz a análise de sentimento dos tweets a uma taxa de 10.000 tweets por segundo.

Com uma SU, esse trabalho do Stream Analytics processará o tráfego? O trabalho pode manter a entrada usando o tamanho de lote padrão de 1000. A latência padrão da análise de sentimento do serviço Web do Machine Learning (com um tamanho de lote padrão de 1000) cria não mais que um segundo de latência.

A latência **geral** ou de ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse trabalho do Stream Analytics, *especialmente* nas chamadas de função do Machine Learning. Com um tamanho de lote de 1000, uma taxa de transferência de 10.000 eventos precisa de aproximadamente 10 solicitações ao serviço Web. Mesmo com uma AU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, o trabalho do Stream Analytics precisará processar 1.000.000 de tweets por segundo. Há duas opções para realizar o aumento da escala:

1. Aumente o tamanho do lote.
2. Particione o fluxo de entrada para processar os eventos simultaneamente.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você terá de gerar mais SUs para ter mais solicitações simultâneas de serviço Web do Machine Learning. Esse número maior de SUs aumenta o **custo** do trabalho.

Vamos examinar a colocação em escala usando as medidas de latência a seguir para cada tamanho de lote:

| Latency | Tamanho do lote |
| --- | --- |
| 200 ms | Lotes de 1.000 eventos ou menos |
| 250 ms | Lotes de 5.000 eventos |
| 300 ms | Lotes de 10.000 eventos |
| 500 ms | Lotes de 25.000 eventos |

1. Usando a primeira opção (**não** Provisionando mais SUs). O tamanho do lote pode ser aumentado para **25.000**. Aumentar o tamanho do lote dessa maneira permitirá que o trabalho processe 1.000.000 de eventos com 20 conexões simultâneas para o serviço Web do Machine Learning (com uma latência de 500 ms por chamada). Portanto, a latência adicional do trabalho do Stream Analytics causada pelas solicitações de função de sentimento aumentaria de **200 ms** para **500 ms** em comparação com as solicitações de serviço Web do Machine Learning. No entanto, **não é possível** aumentar o tamanho do lote infinitamente, pois os serviços Web do Machine Learning exige que o tamanho da carga de uma solicitação seja de 4 MB ou menos e o tempo limite das solicitações de serviço Web se esgota após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200 ms, cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **12 SUs** |5\.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7\.500 |15,000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20,000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Agora, você já deve ter uma boa compreensão de como as funções do Machine Learning funcionam no Stream Analytics. É provável que você também entenda que os jobs do Stream Analytics "extraem" dados de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de obtenção afeta as solicitações de serviço Web do Machine Learning?

Normalmente, o tamanho do lote que definimos para as funções do Machine Learning não será exatamente divisível pelo número de eventos retornados por cada trabalho de "pull" do Stream Analytics. Quando isso ocorre, o serviço Web do Machine Learning é chamado com lotes "parciais". Usar lotes parciais evita a sobrecarga adicional de latência de trabalho em eventos acumulados a cada pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. São elas **FUNCTION REQUESTS**, **FUNCTION EVENTS** e **FAILED FUNCTION REQUESTS**, como mostrado no gráfico a seguir.

![Escalar o Stream Analytics com métricas de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Escalar o Stream Analytics com métricas de funções de Machine Learning")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: O número de solicitações de função.

**EVENTOS DE FUNÇÃO**: O número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: O número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações

Para escalar um trabalho do Stream Analytics com funções do Machine Learning, considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para execução do trabalho do Stream Analytics (e, portanto, o tamanho do lote das solicitações de serviço Web do Machine Learning).
3. As SUs do Stream Analytics provisionadas e o número de solicitações de serviço Web do Machine Learning (os custos adicionais relacionados à função).

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, a [página de perguntas e respostas da Microsoft para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) é um excelente recurso para se obter ajuda adicional da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
