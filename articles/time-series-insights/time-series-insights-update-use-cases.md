---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227740"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casos de uso da Versão Prévia do Azure Time Series Insights

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* Quais são os casos de uso comuns para o Time Series Insights?
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>Introdução

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. É usado para coletar, processar, armazenar, analisar e consultar dados de escala de IoT otimizados para série temporal e altamente contextualizados. O Time Series Insights é ideal para exploração de dados ad hoc e para análise operacional. Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e detecção visual de anomalias

Explore e analise instantaneamente bilhões de eventos para identificar anomalias e descobrir tendências ocultas nos seus dados. O Time Series Insights oferece desempenho em tempo quase real para suas cargas de trabalho de análise de IoT e DevOps.

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* O Time Series Insights não exige nenhuma preparação de dados inicial. 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* Uma vez conectado, você pode visualizar e analisar bilhões de eventos para detecção de anomalias e descobrir tendências ocultas em seus dados.

O Time Series Insights é intuitivo e simples de usar. Você pode interagir com seus dados sem escrever uma única linha de código. There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. Também fornece exploração de seleção e clique para iniciantes.

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

Há três maneiras principais de interagir com os dados armazenados no Time Series Insights:

* A primeira maneira e a mais fácil de começar é com o gerenciador da Versão Prévia do Time Series Insights. Você pode usá-lo para visualizar rapidamente todos os dados de IoT em um só lugar. It provides tools like the heat map to help you spot anomalies in your data. Ele também fornece uma exibição perspectiva. Use-o para comparar até quatro exibições de um ou mais ambientes do Time Series Insights em um único painel. O painel fornece uma exibição de seus dados de série temporal em todos os seus locais. Saiba mais sobre o [Gerenciador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md). Para planejar seu ambiente do Time Series Insights, leia [Planejamento do Time Series Insights](./time-series-insights-update-plan.md).

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. Com apenas algumas linhas de código, você pode criar consultas avançadas. Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. Todos esses elementos existem prontos para uso com o SDK. O SDK também abstrai as APIs de consulta do Time Series Insights. Você pode usá-las para criar predicados do tipo SQL para consultar os dados que você deseja mostrar em um painel. Para soluções de camada de apresentação híbridas, o Time Series Insights oferece URLs parametrizadas. Eles fornecem pontos de conexão perfeita com o gerenciador da Versão Prévia do Time Series Insights para aprofundamentos nos dados.

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* A terceira maneira de começar é usar as APIs eficientes para consultar dados armazenados no Time Series Insights. Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. Todos esses operadores permitem que aplicativos downstream localizem rapidamente tendências e padrões interessantes em seus dados. Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e como impulsionar a eficiência do processo

Use o Time Series Insights para monitorar a integridade, o uso e o desempenho do equipamento em escala. O Time Series Insights fornece uma maneira fácil de medir a eficiência operacional. O Time Series Insights ajuda a gerenciar cargas de trabalho de IoT diversas e imprevisíveis sem sacrificar a ingestão ou o desempenho da consulta.

[![Visão geral](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming e processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio quando combinados com a tecnologia ou a solução certa. Geralmente, essas soluções são uma combinação de vários sistemas. They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

Esses padrões costumam começar com plataformas habilitadas para IoT que ingerem bilhões de eventos de dispositivos e sensores que abrangem várias localidades. Esses sistemas processam e analisam dados de streaming para obterem ações e insights em tempo real. Data is typically archived to warm and cold store for near real-time and batch analytics.

Os dados coletados passam por uma série de processamento para limpeza e contextualização para cenários de análise e consulta downstream. O Azure oferece serviços avançados que podem ser aplicados a cenários de IoT, como fabricação e manutenção de ativo. Esses serviços incluem Time Series Insights, Hub IoT, Hubs de Eventos, Azure Stream Analytics, Azure Functions, Aplicativos Lógicos do Azure, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser obtida da seguinte maneira:

* Ingestão de dados por meio do Hub IoT ou Hubs de Eventos para a melhor segurança, taxa de transferência e latência da categoria.
* Execute o processamento de dados e computação. Os dados ingeridos por funil por meio de serviços, como o Stream Analytics, Aplicativos Lógicos e Azure Functions. O serviço que você usa depende das necessidades específicas de processamento de dados.
* Sinais computados do pipeline de processamento são enviados por push para análise e armazenamento do Time Series Insights.

O Time Series Insights oferece exploração de dados quase em tempo real e insights baseados em ativo para dados históricos. Dependendo das suas necessidades empresariais, os trabalhos de MapReduce e Hive podem ser executados em dados armazenados nos Time Series Insights conectando o Time Series Insights ao Azure HDInsight. Dados armazenados no Time Series Insights estão disponíveis para o Power BI e outros aplicativos de cliente por meio da APIs de consulta superfície pública do Time Series Insights. Esses dados podem ser usados para amplos cenários de inteligência operacional e empresarial.

## <a name="advanced-analytics"></a>Advanced Analytics

Integre com serviços de análise avançados como o Machine Learning e o Azure Databricks. O Time Series Insights recebe dados brutos de milhões de dispositivos. Ele adiciona dados contextuais que podem ser consumidos diretamente por um pacote de serviços de análise do Azure.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

O aprendizado de máquina e a análise avançada consomem e processam grandes volumes de dados. Esses dados são usados para tomar decisões controladas por dados e executar a análise preditiva. Em casos de uso de IoT, algoritmos de análise avançada aprendem com os dados coletados de milhões de dispositivos. Esses dispositivos transmitem dados várias vezes por segundo. Os dados coletados dos dispositivos de IoT são brutos. Eles não têm informações contextuais, como a localização do dispositivo e a unidade da leitura de sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análise avançada.

O Time Series Insights fecha a lacuna entre os dados de IoT e análise avançada de duas maneiras simples e econômicas:

* Primeiro, o Time Series Insights coleta os dados brutos de telemetria de milhões de dispositivos usando o Hub IoT. Ele enriquece dados com informações contextuais e transforma os dados em um formato parquet. Esse formato pode facilmente se integrar a outros serviços de análise avançada, como Machine Learning, Azure Databricks e aplicativos de terceiros.

    O Time Series Insights pode servir como a fonte da verdade para todos os dados em toda a organização. Ele cria um repositório central para cargas de trabalho de análise downstream consumirem. Porque o Time Series Insights é um serviço de armazenamento quase em tempo real, modelos de análise avançada podem aprender continuamente com os dados de telemetria de IoT recebidos. Como resultado, os modelos podem fazer previsões mais precisas.

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. Esse procedimento ajuda as organizações a otimizar e ajustar seus modelos. O Time Series Insights torna simples a visualizar o fluxo de dados de telemetria no mesmo plano que modelo treinado produz. Dessa forma, ele ajuda as equipes de ciência de dados a detectar anomalias e identificar padrões.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre o [Gerenciador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md).
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* Leia a documentação do [cliente do Time Series Insights](https://github.com/Microsoft/tsiclient).
