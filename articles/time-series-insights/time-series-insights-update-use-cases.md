---
title: Pré-visualização de casos de uso - Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre os casos de uso de visualização de séries do Azure Time.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087383"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casos de uso da Versão Prévia do Azure Time Series Insights

Este artigo resume vários casos de uso comum para a Visualização de Insights da série do tempo do Azure. As recomendações deste artigo servem como ponto de partida para desenvolver suas aplicações e soluções com o Time Series Insights.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comuns para o Time Series Insights?
* Quais são os benefícios de usar o Time Series Insights para [exploração de dados e detecção de anomalias visuais?](#data-exploration-and-visual-anomaly-detection)
* Quais são os benefícios de usar o Time Series Insights para [análise operacional e eficiência de processos?](#operational-analysis-and-driving-process-efficiency)
* Quais são os benefícios de usar o Time Series Insights para [análises avançadas?](#advanced-analytics)

Uma visão geral desses cenários de uso é descrita nas seções a seguir.

## <a name="introduction"></a>Introdução

O Azure Time Series Insights é uma oferta de plataforma como serviço. É usado para coletar, processar, armazenar, analisar e consultar dados de escala de IoT otimizados para série temporal e altamente contextualizados. O Time Series Insights é ideal para exploração de dados ad hoc e para análise operacional. Time Series Insights é uma oferta de serviço exclusivamente extensível e personalizada que atende às amplas necessidades de implantações industriais de IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e detecção visual de anomalias

Explore e analise instantaneamente bilhões de eventos para identificar anomalias e descobrir tendências ocultas nos seus dados. O Time Series Insights oferece desempenho em tempo quase real para suas cargas de trabalho de análise de IoT e DevOps.

[![Explorador de dados](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A maioria dos clientes concorda que o tempo mínimo necessário para obter insights é um dos recursos de destaque do Time Series Insights:

* O Time Series Insights não exige nenhuma preparação de dados inicial. 
* Ele funciona rapidamente para conectá-lo a bilhões de eventos em seu Azure IoT Hub ou Azure Event Hubs em minutos. 
* Uma vez conectado, você pode visualizar e analisar bilhões de eventos para detecção de anomalias e descobrir tendências ocultas em seus dados.

O Time Series Insights é intuitivo e simples de usar. Você pode interagir com seus dados sem escrever uma única linha de código. Também não há um novo idioma que você é obrigado a aprender, embora o Time Series Insights forneça uma linguagem de consulta baseada em texto granular para usuários avançados que estejam familiarizados com o SQL. Também fornece exploração de seleção e clique para iniciantes.

Os clientes podem aproveitar a velocidade para diagnosticar problemas relacionados a ativos rapidamente. Eles podem realizar a análise de DevOps para chegar à causa raiz de um bug em uma solução de IoT. Eles também podem identificar áreas a serem sinalizadas para uma investigação mais aprofundada como parte de suas iniciativas de ciência de dados. 

Há três maneiras principais de interagir com os dados armazenados no Time Series Insights:

* A primeira maneira e a mais fácil de começar é com o gerenciador da Versão Prévia do Time Series Insights. Você pode usá-lo para visualizar rapidamente todos os dados de IoT em um só lugar. Ele fornece ferramentas como o mapa de calor para ajudá-lo a detectar anomalias em seus dados. Ele também fornece uma exibição perspectiva. Use-o para comparar até quatro exibições de um ou mais ambientes do Time Series Insights em um único painel. O painel fornece uma exibição de seus dados de série temporal em todos os seus locais. Saiba mais sobre o [Gerenciador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md). Para planejar seu ambiente do Time Series Insights, leia [Planejamento do Time Series Insights](./time-series-insights-update-plan.md).

* A segunda maneira de começar é usar o JavaScript SDK para incorporar rapidamente gráficos e gráficos poderosos em sua aplicação web. Com apenas algumas linhas de código, você pode criar consultas avançadas. Use-os para preencher gráficos de linha, gráficos de tortas, gráficos de barras, mapas de calor, grades de dados e muito mais. Todos esses elementos existem prontos para uso com o SDK. O SDK também abstrai as APIs de consulta do Time Series Insights. Você pode usá-las para criar predicados do tipo SQL para consultar os dados que você deseja mostrar em um painel. Para soluções de camada de apresentação híbridas, o Time Series Insights oferece URLs parametrizadas. Eles fornecem pontos de conexão perfeita com o gerenciador da Versão Prévia do Time Series Insights para aprofundamentos nos dados.

  * Leia sobre a [biblioteca de clientes Do Time Series Insights JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e a documentação [do cliente Time Series Insights](https://github.com/Microsoft/tsiclient) para saber mais sobre o JavaScript SDK.

  * Saiba mais sobre o compartilhamento de URLs e a nova UI revisando [dados do Visualize no explorador de visualização do Azure Time Series Insights](time-series-insights-update-explorer.md).

* A terceira maneira de começar é usar as APIs eficientes para consultar dados armazenados no Time Series Insights. Time Series Insights tem operadores `to` `first`temporais `last`como `from`, , e . Tem agregações e transformações `average`como, `min` `split by`e `order by` `DateHistogram` `max`. Também possui operadores de `has`filtragem como, `in`e `and` `or` `greater than` `REGEX`. Todos esses operadores permitem que aplicativos downstream localizem rapidamente tendências e padrões interessantes em seus dados. Use-os para preencher visualizações caseiras para detectar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e como impulsionar a eficiência do processo

Use o Time Series Insights para monitorar a integridade, o uso e o desempenho do equipamento em escala. O Time Series Insights fornece uma maneira fácil de medir a eficiência operacional. O Time Series Insights ajuda a gerenciar cargas de trabalho de IoT diversas e imprevisíveis sem sacrificar a ingestão ou o desempenho da consulta.

[![Visão geral](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming e processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio quando combinados com a tecnologia ou a solução certa. Geralmente, essas soluções são uma combinação de vários sistemas. Eles permitem a exploração e análise de dados que mudam constantemente, especialmente no reino ioT, e compartilham um padrão comum.

Esses padrões costumam começar com plataformas habilitadas para IoT que ingerem bilhões de eventos de dispositivos e sensores que abrangem várias localidades. Esses sistemas processam e analisam dados de streaming para obterem ações e insights em tempo real. Os dados são normalmente arquivados em lojas quentes e frias para análises quase em tempo real e em lote.

Os dados coletados passam por uma série de processamento para limpeza e contextualização para cenários de análise e consulta downstream. O Azure oferece serviços avançados que podem ser aplicados a cenários de IoT, como fabricação e manutenção de ativo. Esses serviços incluem Time Series Insights, Hub IoT, Hubs de Eventos, Azure Stream Analytics, Azure Functions, Aplicativos Lógicos do Azure, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser obtida da seguinte maneira:

* Ingestão de dados por meio do Hub IoT ou Hubs de Eventos para a melhor segurança, taxa de transferência e latência da categoria.
* Execute o processamento de dados e computação. Os dados ingeridos por funil por meio de serviços, como o Stream Analytics, Aplicativos Lógicos e Azure Functions. O serviço que você usa depende das necessidades específicas de processamento de dados.
* Sinais computados do pipeline de processamento são enviados por push para análise e armazenamento do Time Series Insights.

O Time Series Insights oferece exploração de dados quase em tempo real e insights baseados em ativo para dados históricos. Dependendo das suas necessidades empresariais, os trabalhos de MapReduce e Hive podem ser executados em dados armazenados nos Time Series Insights conectando o Time Series Insights ao Azure HDInsight. Dados armazenados no Time Series Insights estão disponíveis para o Power BI e outros aplicativos de cliente por meio da APIs de consulta superfície pública do Time Series Insights. Esses dados podem ser usados para amplos cenários de inteligência operacional e empresarial.

## <a name="advanced-analytics"></a>Análise avançada

Integre com serviços de análise avançados como o Machine Learning e o Azure Databricks. O Time Series Insights recebe dados brutos de milhões de dispositivos. Ele adiciona dados contextuais que podem ser consumidos diretamente por um pacote de serviços de análise do Azure.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

O aprendizado de máquina e a análise avançada consomem e processam grandes volumes de dados. Esses dados são usados para tomar decisões controladas por dados e executar a análise preditiva. Em casos de uso de IoT, algoritmos de análise avançada aprendem com os dados coletados de milhões de dispositivos. Esses dispositivos transmitem dados várias vezes por segundo. Os dados coletados dos dispositivos de IoT são brutos. Eles não têm informações contextuais, como a localização do dispositivo e a unidade da leitura de sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análise avançada.

O Time Series Insights fecha a lacuna entre os dados de IoT e análise avançada de duas maneiras simples e econômicas:

* Primeiro, o Time Series Insights coleta os dados brutos de telemetria de milhões de dispositivos usando o Hub IoT. Ele enriquece dados com informações contextuais e transforma os dados em um formato parquet. Esse formato pode facilmente se integrar a outros serviços de análise avançada, como Machine Learning, Azure Databricks e aplicativos de terceiros.

    O Time Series Insights pode servir como a fonte da verdade para todos os dados em toda a organização. Ele cria um repositório central para cargas de trabalho de análise downstream consumirem. Porque o Time Series Insights é um serviço de armazenamento quase em tempo real, modelos de análise avançada podem aprender continuamente com os dados de telemetria de IoT recebidos. Como resultado, os modelos podem fazer previsões mais precisas.

* Em segundo lugar, a saída de modelos de aprendizado de máquina e predição pode ser alimentada em Insights de Séries Tempopara visualizar e armazenar seus resultados. Esse procedimento ajuda as organizações a otimizar e ajustar seus modelos. O Time Series Insights torna simples a visualizar o fluxo de dados de telemetria no mesmo plano que modelo treinado produz. Dessa forma, ele ajuda as equipes de ciência de dados a detectar anomalias e identificar padrões.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Gerenciador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md).
* Leia [o Planejamento de visualização da série](./time-series-insights-update-plan.md) do tempo para planejar seu ambiente.
* Leia a documentação do [cliente do Time Series Insights](https://github.com/Microsoft/tsiclient).
