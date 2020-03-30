---
title: Painel de insights de marketplace em análises de mercado comercial no Partner Center
description: Acesse um resumo das análises web do marketplace, que permite que os editores meçam o engajamento do cliente nas vitrines AppSource e Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275842"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Painel de insights de marketplace em análises de mercado comercial

Este artigo fornece informações sobre o painel Marketplace Insights no Partner Center. Este painel exibe um resumo das análises web do marketplace, o que permite que os editores medem o engajamento do cliente para suas respectivas páginas de detalhes de produtos listadas nas vitrines do marketplace: AppSource e Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Painel Insights do Marketplace

Para acessar o **painel marketplace insights** no Partner Center, abra a **[guia Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Mercado Comercial.

Você pode visualizar representações gráficas dos seguintes itens:  

- [Resumo do Marketplace Insights](#marketplace-insights-summary)
- [Visitas de página por geografia](#page-visits-by-geography)  
- [Visitas de página versus tendência de visitantes únicos](#page-visits-versus-unique-visitors-trend)
- [Chamada à ação versus visitantes únicos com CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Visitas de página e chamada à ação por ofertas](#page-visits-and-calls-to-action-by-offers)
- [Tendência percentual de chamada à ação](#call-to-action-percentage-trend)
- [Visitas de página e chamadas à ação por domínios de referência](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabela de detalhes do Marketplace Insights](#marketplace-insights-details-table)

>[!NOTE]
> Para definições detalhadas da terminologia de análise, consulte [perguntas freqüentes e terminologia para análises de mercado comercial](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Layout do painel de insights

Você pode visualizar as métricas do Marketplace de várias maneiras:

- Guias de vitrine
- Filtros de página
- Filtros de data

**Guias do Storefront**: Você pode visualizar as métricas de suas ofertas separadamente através das guias AppSource & Azure Marketplace. Selecione as ofertas na lista de isento de oferta à direita para ver uma visualização das métricas para as ofertas selecionadas. Por padrão, todas as ofertas são selecionadas.

![O painel de controle do Partner Center Insights oferece lista de parada](./media/insights-offer-dropdown.png)

**Filtros de página insights**: Esses filtros são aplicados no nível de oferta (página de detalhes do produto). Você pode selecionar vários filtros para os critérios que deseja visualizar. Este filtro se aplica a toda a seção Marketplace Insights, incluindo gráficos e detalhes.

![Filtros da página do painel do Partner Center Insights](./media/insights-page-filter.png)

- Oferta Os nomes são listados apenas para as ofertas que têm visitas de página na faixa de data selecionada.  
- A seleção padrão é 'Todos' para cada uma das opções de filtro
- Os filtros aplicados mostram a contagem de seleções para as escolhas feitas. Os filtros aplicados não serão exibidos para a seleção padrão 'Todos'.

![Filtros do Partner Center Insights aplicados](./media/insights-page-filter-two.png)

**Filtros de data de insights**: Este filtro se aplica a toda a seção Marketplace Insights. Os filtros podem incluir intervalos de datapré-determinados ou um intervalo de datas personalizado.

![Filtros de data do Partner Center Insights](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Resumo do Marketplace Insights

A seção Resumo de insights do Marketplace exibe uma contagem de visitas de **página,** **chamadas à ação**e **visitantes únicos** para a faixa de data selecionada.

### <a name="page-visits"></a>Visitas de página

Esse número representa a contagem de sessões de usuário distintas na página de oferta (página de detalhes do produto) para uma faixa de data selecionada. O indicador percentual vermelho/verde representa o crescimento % das visitas de página. O gráfico de tendências representa a contagem mensal de visitas de página.

### <a name="unique-visitors"></a>Visitantes únicos

Este número representa a contagem de visitantes distinta durante a faixa de data selecionada para as ofertas selecionadas no filtro da página. Um visitante que visitou uma ou mais páginas de detalhes do produto será contado como um visitante único.

### <a name="call-to-action"></a>Plano de ação

Este número representa a contagem de cliques do botão **Call to Action** concluído na página de oferta (página de detalhes do produto). **As chamadas para ação** são contadas quando os botões **Get it now**, **Free trial**, **Contact me**e **Test drive** são selecionados.

![Partner Center Insights chamam para o resumo da ação](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visitas de página por geografia

O mapa de calor abaixo exibe a contagem de visitas de **página,** **chamadas para ação**e **visitantes únicos de acordo com o país do cliente.** As visitas de páginas mais altas são representadas por cores mais escuras do mapa e as visitas de páginas mais baixas são representadas por cores mais claras do mapa.

![Partner Center Insights spread geográfico](./media/insights-geography.png)

O mapa de calor inclui as seguintes capacidades:

- O mapa de calor possui uma grade suplementar para visualizar os detalhes das visitas ao **Page,** **chamadas para ação** e **visitantes únicos** em um local específico; você pode ampliar para um local específico, se preferir.  
- **Países espalhados** é a contagem de todos os países de onde seus clientes relataram visitas de página durante a faixa de data selecionada.
- Você pode pesquisar e selecionar um país na grade para ampliar a localização no mapa. Reverter para a exibição original selecionando **Home** no mapa.

## <a name="page-visits-versus-unique-visitors-trend"></a>Visitas de página versus tendência de visitantes únicos

As colunas abaixo representam a contagem de visitas mensais de página, que são exibidas no eixo Y (eixo no lado esquerdo do gráfico). A linha de tendência representa a tendência mensal de visitantes únicos, que é exibida no Eixo Y secundário (eixo no lado direito do gráfico), para suas ofertas publicadas nas vitrines: Azure Marketplace e AppSource.

![Visitas de página do Partner Center Insights versus tendência de visitantes únicos](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Chamada à ação versus visitantes únicos com CTAs

As colunas empilhadas representam chamadas mensais à ação (CTA), que são divididas por tipos de CTA **(Get it now**, **Contact me**, e **Free Trial)** e plotadas no eixo Y (eixo no lado esquerdo da página). A linha de tendência representa a tendência mensal de visitantes únicos com CTAs, que é exibido no Y-Axis secundário (eixo no lado direito do gráfico), para suas ofertas publicadas no Azure Marketplace e AppSource.

![O Partner Center Insights chama a ação contra visitantes únicos com CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visitas de página e chamadas à ação por ofertas

O gráfico de tortas externas representa a divisão das visitas de **página** com base em ofertas que você publicou no mercado e selecionadas no filtro. O gráfico interno representa a divisão **Chamadas para ação** para as mesmas ofertas.

![A página do Partner Center Insights visita e chama a ação por ofertas](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Tendência percentual de chamada à ação

A **tendência de percentual de call to action** apresenta o percentual de CTA para as ofertas publicadas no mercado. CTA % = (CTAs/visitas de página) * 100.

![O Partner Center Insights chama a tendência de porcentagem de ação](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visitas de página e chamadas à ação por domínios de referência

O gráfico abaixo apresenta os 50 principais domínios de referência. A seleção de um domínio de referência específico mostra a tendência mensal de visitas de página e chamadas para ação no gráfico à direita.

![A página do Partner Center Insights visita e chama a ação por domínios de referência e campanhas](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Marketplace Insights

Esta tabela fornece uma exibição da lista das visitas à página e das chamadas para a ação de suas ofertas selecionadas classificadas por data.

![Tabela de detalhes do Partner Center Insights](./media/insights-details-page.png)

- Os dados podem ser extraídos para um arquivo CSV se a contagem de registros for menor que 1000.
- Se a contagem de registros for superior a 1000, os dados exportados serão colocados assíncronamente em uma página de downloads pelos próximos 30 dias.
- Os filtros podem ser aplicados para exibir os dados que você está interessado. Os dados podem ser filtrados por nomes de ofertas e nomes de campanha.  

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para obter gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para sua oferta, consulte [Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, consulte [Orders Dashboard em análises de mercado comercial](./orders-dashboard.md).
- Para a Máquina Virtual (VM) oferece métricas de uso e faturamento medido, consulte [O Painel de Uso em análises de mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análises de Mercado Comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O Painel de Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análises de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas e terminologias freqüentes para análises de mercado comercial](./faq-terminology.md).
