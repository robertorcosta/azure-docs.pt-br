---
title: Dimensionar funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que usam funções de Machine Learning, configurando as unidades de particionamento e transmissão.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dedffab0b17515cedc54569d5debf6d29b273644
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458739"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Dimensionar seu trabalho de Stream Analytics com funções Azure Machine Learning Studio (clássicas)

Este artigo discute como dimensionar com eficiência Azure Stream Analytics trabalhos que usam Azure Machine Learning funções. Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?

Uma função do Machine Learning no Stream Analytics pode ser usada como uma chamada de função normal na linguagem de consulta do Stream Analytics. Em segundo plano, no entanto, essas chamadas de função são realmente Azure Machine Learning solicitações de serviço Web.

Você pode melhorar a taxa de transferência de Machine Learning solicitações de serviço Web por meio de "envio em lote" de várias linhas na mesma chamada à API de serviço Web. Esse agrupamento é chamado de mini-lote. Para obter mais informações, consulte [Azure Machine Learning Studio (clássico) serviços Web](../machine-learning/studio/consume-web-services.md). O suporte para Azure Machine Learning Studio (clássico) no Stream Analytics está em versão prévia.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar seu trabalho do Stream Analytics com funções de Machine Learning

Há dois parâmetros para configurar a função de Machine Learning usada pelo seu trabalho de Stream Analytics:

* Tamanho do lote das chamadas de função Machine Learning.
* O número de unidades de streaming (SUs) provisionadas para o trabalho de Stream Analytics.

Para determinar os valores apropriados para o SUs, decida se deseja otimizar a latência do trabalho de Stream Analytics ou a taxa de transferência de cada SU. O SUs sempre pode ser adicionado a um trabalho para aumentar a taxa de transferência de uma consulta de Stream Analytics bem particionada. O SUs adicional aumenta o custo da execução do trabalho.

Determine a *tolerância* de latência para seu trabalho de Stream Analytics. Aumentar o tamanho do lote aumentará a latência de suas solicitações de Azure Machine Learning e a latência do trabalho de Stream Analytics.

Aumentar o tamanho do lote permite que o trabalho de Stream Analytics processe **mais eventos** com o **mesmo número** de Machine Learning solicitações de serviço da Web. O aumento da latência de serviço Web de Machine Learning geralmente é sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico para um serviço Web Machine Learning em qualquer situação em questão. O tamanho de lote padrão para solicitações de serviço Web é 1000. Você pode alterar esse tamanho padrão usando a [API REST do Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente do PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir sobre um tamanho de lote, você pode definir o número de unidades de streaming (SUs), com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SUs Obtém 20 conexões simultâneas com o serviço Web Machine Learning. No entanto, um trabalho SU e 3 trabalhos SU recebem 20 conexões simultâneas.  

Se seu aplicativo gerar 200.000 eventos por segundo e o tamanho do lote for 1000, a latência do serviço Web resultante será de 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações para o serviço Web Machine Learning a cada segundo. Com 20 conexões, o trabalho de Stream Analytics pode processar eventos 20.000 em eventos de 200 MS e 100.000 em um segundo.

Para processar eventos 200.000 por segundo, o trabalho de Stream Analytics precisa de 40 conexões simultâneas, que vão para 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho do Stream Analytics para o ponto de extremidade de serviço Web do Machine Learning – a cada 6 SUs há, no máximo, 20 conexões simultâneas com o serviço Web do Machine Learning.

![Dimensionar Stream Analytics com funções de Machine Learning dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Dimensionar Stream Analytics com funções de Machine Learning dois exemplos de trabalho")

Em geral, sendo ***B*** o tamanho do lote e ***L*** a latência do serviço Web com o tamanho do lote B em milissegundos, a produtividade de um trabalho do Stream Analytics com ***N*** SUs será:

![Stream Analytics de escala com fórmula de funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula para escalar o Stream Analytics com funções de Machine Learning")

Você também pode configurar o ' máximo de chamadas simultâneas ' no serviço Web do Machine Learning. É recomendável definir esse parâmetro para o valor máximo (200 no momento).

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

Vamos examinar a configuração necessária para criar um trabalho Stream Analytics, que faz a análise de sentimentos de tweets a uma taxa de 10.000 tweets por segundo.

Usando um SU, seria possível que essa Stream Analytics o trabalho manipule o tráfego? O trabalho pode acompanhar a entrada usando o tamanho de lote padrão de 1000. A latência padrão da análise de sentimentos Machine Learning serviço Web (com um tamanho de lote padrão de 1000) não cria mais que um segundo de latência.

A latência **geral** ou ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse trabalho do Stream Analytics, *especialmente* nas chamadas de função do Machine Learning. Com um tamanho de lote de 1000, uma taxa de transferência de 10.000 eventos leva cerca de 10 solicitações para o serviço Web. Mesmo com uma AU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, o trabalho do Stream Analytics precisará processar 1.000.000 de tweets por segundo. Há duas opções para realizar o aumento da escala:

1. Aumente o tamanho do lote.
2. Particione o fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você precisará provisionar mais SUs para ter mais solicitações simultâneas de serviço Web de Machine Learning. Esse número maior de SUs aumenta o **custo**do trabalho.

Vamos examinar o dimensionamento usando as seguintes medidas de latência para cada tamanho de lote:

| Latência | Tamanho do lote |
| --- | --- |
| 200 ms | 1000-lotes de eventos ou abaixo |
| 250 ms | 5\.000-lotes de eventos |
| 300 ms | 10.000-lotes de eventos |
| 500 ms | 25.000-lotes de eventos |

1. Usando a primeira opção (**não** Provisionando mais SUs). O tamanho do lote pode ser aumentado para **25.000**. O aumento do tamanho do lote dessa maneira permitirá que o trabalho processe eventos 1 milhão com 20 conexões simultâneas com o serviço Web Machine Learning (com uma latência de 500 MS por chamada). Portanto, a latência adicional do trabalho do Stream Analytics causada pelas solicitações de função de sentimento aumentaria de **200 ms** para **500 ms** em comparação com as solicitações de serviço Web do Machine Learning. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, pois os serviços web do Machine Learning exigem que o tamanho da carga de uma solicitação seja de 4 MB ou menor, e o tempo limite das solicitações de serviço web após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200 ms, cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **12 SUs** |5\.000 |10.000 |40.000 |60.000 |100 mil |
| **18 SUs** |7\.500 |15,000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20,000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Agora, você já deve ter uma boa compreensão de como as funções do Machine Learning funcionam no Stream Analytics. É provável que você também entenda que os jobs do Stream Analytics "extraem" dados de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de obtenção afeta as solicitações de serviço Web do Machine Learning?

Normalmente, o tamanho do lote que definimos para as funções de Aprendizado da Máquina não será exatamente divisível pelo número de eventos retornados por cada tarefa "pull" do Stream Analytics. Quando isso ocorre, o serviço Web do Machine Learning é chamado com lotes "parciais". O uso de lotes parciais evita a incorrência de sobrecarga de latência de trabalho adicional na União de eventos de pull para pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. Eles são **solicitações de função**, **eventos de função** e solicitações de **função com falha**, conforme mostrado no gráfico abaixo.

![Dimensionar Stream Analytics com métricas de funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Escalar o Stream Analytics com métricas de funções de Machine Learning")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: o número de solicitações de função.

**EVENTOS DE FUNÇÃO**: o número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: o número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações

Para dimensionar um trabalho de Stream Analytics com funções Machine Learning, considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote das solicitações de serviço Web do Machine Learning).
3. O SUs provisionado Stream Analytics e o número de Machine Learning solicitações de serviço da Web (os custos adicionais relacionados à função).

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) é um excelente recurso para obter ajuda adicional da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
