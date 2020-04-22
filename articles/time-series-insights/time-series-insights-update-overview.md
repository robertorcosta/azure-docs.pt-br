---
title: 'Visão geral: O que é o Azure Time Series Insights Preview? - Azure Time Series Insights | Microsoft Docs'
description: Saiba mais as alterações, aprimoramentos e recursos na Versão Prévia do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 73244a635bbf14efcf33f1b978db14e9e2589581
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271056"
---
# <a name="what-is-azure-time-series-insights-preview"></a>O que é o Azure Time Series Insights Preview?

O Azure Time Series Insights Preview é uma oferta de plataforma como serviço (PaaS) de ponta a ponta. Você pode usá-lo para coletar, processar, armazenar, analisar e consultar dados na escala da Internet das Coisas (IoT) – dados altamente contextualizados e otimizados para séries temporais. 

O Time Series Insights é ideal para explorar dados ad hoc e para análises operacionais. É uma oferta de serviço extensível e personalizada que atende às amplas necessidades de implantações industriais de IoT.

## <a name="video"></a>Vídeo

Saiba mais sobre a versão prévia do Azure Time Series Insights.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definição de dados de IoT

Os dados industriais de IoT em organizações com uso intensivo de ativos geralmente carecem de consistência estrutural, devido à natureza variada de dispositivos e sensores em um ambiente industrial. Os dados desses fluxos são caracterizados por lacunas significativas e, às vezes, mensagens corrompidas e leituras falsas. Os dados da IoT geralmente são significativos no contexto de entradas de dados adicionais provenientes de fontes primárias ou de terceiros, como CRM ou ERP, que adicionam contexto para fluxos de trabalho de ponta a ponta. As entradas de fontes de dados de terceiros, como dados climáticos, podem ajudar a aumentar os fluxos de telemetria em uma determinada instalação. 

Tudo isso implica que apenas uma fração dos dados é usada para fins operacionais e comerciais, e a análise requer contextualização. Os dados industriais costumam ser históricos, para gerar análises detalhadas em períodos de tempo mais longos, de modo a entender e correlacionar tendências. Transformar dados de IoT coletados em insights acionáveis requer: 

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise.
* Uma estrutura para navegar e entender os dados, ou seja, para normalizar e contextualizá-los.
* Armazenamento econômico para retenção longa ou infinita de dados processados (ou derivados) e dados brutos.

Esses dados fornecem informações consistentes, abrangentes, atuais e corretas para análises e relatórios de negócios.

A imagem a seguir mostra um fluxo de dados de IoT típico.

[![Fluxo de dados de IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights para IoT industrial

O cenário de IoT é diversificado, com clientes em vários segmentos da indústria, incluindo os setores manufatureiros, automotivos, energéticos, de serviços públicos, edifícios inteligentes e consultoria. Em toda essa ampla gama de mercado de IoT industrial, as soluções nativas da nuvem que fornecem análises abrangentes direcionadas a dados de IoT em larga escala ainda estão evoluindo. 

O Azure Time Series Insights atende a essa necessidade do mercado, fornecendo uma solução de análise de IoT de ponta a ponta completa, com modelagem semântica avançada para contextualização de dados de séries temporais, informações com base em ativos, e uma melhor experiência do usuário para descoberta, tendências, detecção de anomalias e inteligência operacional. 

Com uma plataforma de análise operacional avançada e combinada com nossos recursos interativos de exploração de dados, você pode usar o Time Series Insights para obter mais valor dos dados coletados dos ativos da IoT. A oferta da versão prévia dá suporte a: 

* solução de armazenamento em várias camadas com suporte a análises de categorização de dados mornos e frios, oferecendo aos clientes a opção de rotear os dados entre a categorização morna e fria para análise interativa de dados mornos, assim como gerar inteligência operacional sobre décadas de dados históricos. 

    *    Uma solução de análise de dados mornos altamente interativa para executar um número grande e frequente de consultas em dados coletados em um período mais curto 
    *    Um data lake de série temporal escalonável, de alto desempenho e otimizado para custo com base no Armazenamento do Azure que permite que os clientes criem tendências de anos de dados de série temporal em segundos. 

* Suporte ao modelo semântico que descreve o domínio e os metadados associados aos sinais derivados e brutos de ativos e dispositivos.

* A plataforma flexível de análise para armazenar dados históricos de séries temporais na conta de Armazenamento do Microsoft Azure de propriedade dos clientes, permitindo que eles sejam proprietários de seus dados de IoT. Os dados são armazenados no formato Apache Parquet de código aberto, que permite conectividade e interoperabilidade em uma variedade de cenários de dados, incluindo análise preditiva, aprendizado de máquina e outros cálculos personalizados feitos usando tecnologias conhecidas, como Spark, Databricks e Jupyter.

* Análise avançada com APIs de consulta aprimoradas, e uma experiência do usuário que combina insights de dados baseados em ativos com análises de dados avançadas e ad hoc com suporte para interpolação, funções escalares e agregadas, variáveis categóricas, gráficos de dispersão e sinais de séries temporais de mudança de tempo para análises detalhadas.

*    Plataforma de nível empresarial para dar suporte a necessidades de escala, desempenho, segurança e confiabilidade de nossos clientes de IoT empresariais.

* Suporte de extensibilidade e integração para análises de ponta a ponta. O Time Series Insights fornece uma plataforma de análise extensível para uma variedade de cenários de dados. O conector do Power BI para Time Series Insights permite que os clientes tragam as consultas do Time Series Insights diretamente para o Power BI, de modo a obter uma visão unificada de suas análises de BI e séries temporais em um único painel de controle.

O diagrama a seguir mostra o fluxo de dados de alto nível.

  [![Principais funcionalidades](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

O Azure Time Series Insights fornece um modelo de preço escalonável conforme o uso para consulta, armazenamento (dados e metadados) e processamento de dados, permitindo que os clientes ajustem seu uso para atender às demandas de negócios. 
 
Com a introdução desses principais recursos industriais de IoT, o Time Series Insights também oferece os principais benefícios a seguir.  

| | |
| ---| ---|
| Armazenamento em várias camadas para dados de série temporal em escala de IoT | Com um pipeline de processamento de dados compartilhado para ingestão de dados, é possível ingerir os dados em lojas categorizados como frios e mornos. Use o armazenamento morno para consultas interativas e o armazenamento frio para armazenar grandes volumes de dados. Para saber mais sobre como aproveitar as consultas baseadas em ativos de alto desempenho, confira [consultas](./time-series-insights-update-tsq.md). |
| Modelo de série temporal para contextualizar dados telemétricos brutos e derivar insights baseados em ativos | Você pode usar o modelo de série temporal para criar instâncias, hierarquias, tipos e variáveis para seus dados de série temporal. Para saber mais sobre o modelo de série temporal, confira [Modelo de série temporal](./time-series-insights-update-tsm.md).  |
| Integração perfeita e contínua com outras soluções de dados | Os dados no armazenamento frio do Time Series Insights são [armazenados](./time-series-insights-update-storage-ingress.md) em arquivos de código aberto Apache Parquet. Isso permite a integração de dados com outras soluções de dados, de fontes primárias ou de terceiros, para cenários que incluem inteligência comercial, aprendizado de máquina avançado e análise preditiva. |
| Exploração de dados quase em tempo real | A experiência do usuário do [Explorer do Azure Time Series Insights Preview](./time-series-insights-update-explorer.md) fornece uma visualização de todos os dados de streaming por meio do pipeline de ingestão. Depois de conectar uma fonte de eventos, é possível exibir, explorar e consultar dados de eventos. Dessa forma, você pode validar se um dispositivo emite os dados conforme o esperado. Você também pode monitorar um ativo de IoT em relação à integridade, à produtividade e à eficiência geral. | 
| Extensibilidade e integração | A integração do Conector do Power BI para Azure Time Series Insights está disponível diretamente na experiência do usuário do Time Series Explorer por meio da opção **Exportar**, permitindo que os clientes exportem as consultas de séries temporais criadas com a nossa experiência do usuário diretamente para a área de trabalho do Power BI e visualize seus gráficos de séries temporais juntamente com outras análises de BI. Isso abre a porta para uma nova classe de cenários para empresas de IoT industriais que investiram no Power BI, fornecendo um único painel de controle sobre análises de várias fontes de dados, incluindo séries temporais da IoT. | 
| Criação de aplicativos personalizados na plataforma do Time Series Insights | O Time Series Insights dá suporte ao [SDK do JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). O SDK fornece controles avançados e acesso simplificado às consultas. Use o SDK para criar aplicativos IoT personalizados no Time Series Insights para atender às suas necessidades de negócios. Você também pode usar as [APIs de consulta](./time-series-insights-update-tsq.md) do Time Series Insights diretamente para levar os dados a aplicativos de IoT personalizados. |

## <a name="next-steps"></a>Próximas etapas

Introdução à Versão Prévia do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Guia de Início Rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre os casos de uso:

> [!div class="nextstepaction"]
> [Casos de uso do Azure Time Series Insights Preview](./time-series-insights-update-use-cases.md)