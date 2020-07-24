---
title: Insights do Marketplace-Microsoft Commercial Marketplace, Microsoft AppSource e Azure Marketplace
description: Acesse um resumo da análise Web do marketplace, que permite que você meça o envolvimento do cliente no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: c72f3c9cce19896557fa9f4114b935fcc5ca8dc2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128940"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>Painel Insights do Marketplace no Partner Center

Este artigo fornece informações sobre o painel Insights do Marketplace no Partner Center. Este painel exibe um resumo do Marketplace Web Analytics que permite que os editores meçam o envolvimento do cliente para suas respectivas páginas de detalhes do produto listadas nas vitrines do Marketplace comercial: Microsoft AppSource e Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Painel Insights do Marketplace

Para acessar o **painel Insights do Marketplace** no Partner Center, abra a **[guia Análise](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Marketplace comercial.

Você pode ver as representações gráficas dos seguintes itens:  

- [Resumo do Insights do Marketplace](#marketplace-insights-summary)
- [Visitas à página por região geográfica](#page-visits-by-geography)  
- [Visitas à página versus tendência de visitantes exclusivos](#page-visits-versus-unique-visitors-trend)
- [Plano de ação (CTA) versus visitantes exclusivos com CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Visitas à página e plano de ação por ofertas](#page-visits-and-calls-to-action-by-offers)
- [Tendência percentual do plano de ação](#call-to-action-percentage-trend)
- [Visitas à página e planos de ação por domínios de referência](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabela de detalhes do Insights do Marketplace](#marketplace-insights-details-table)

A latência máxima entre os usuários que visitam ofertas no Azure Marketplace ou AppSource e relatórios no Partner Center é de 48 horas.

>[!NOTE]
> Para obter definições detalhadas da terminologia de análise, confira [Perguntas frequentes e terminologia para análise do marketplace comercial](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Painel de informações do Insights

Veja as métricas do Marketplace comercial de várias maneiras:

- Guias da vitrine
- Filtros de página
- Filtros de data

**Guias da vitrine**: Você pode exibir as métricas de suas ofertas separadamente por meio das guias AppSource e Azure Marketplace. Selecione as ofertas na lista suspensa da oferta à direita para ver uma visualização das métricas das ofertas selecionadas. Por padrão, todas as ofertas são selecionadas.

![Lista suspensa da oferta no painel do Insights do Partner Center](./media/insights-offer-dropdown.png)

**Filtros de página do Insights**: Esses filtros são aplicados no nível da oferta (página de detalhes do produto). É possível selecionar vários filtros para os critérios que você deseja exibir. Esse filtro se aplica a toda a seção Insights do Marketplace, incluindo gráficos e detalhes.

![Filtros de página do painel Insights do Partner Center](./media/insights-page-filter.png)

- Os nomes das ofertas são listados apenas para as ofertas que têm visitas à página no intervalo de datas selecionado.  
- A seleção padrão é “Todos” para cada uma das opções de filtro
- Os filtros aplicados mostram a contagem de seleções para as escolhas feitas. Os filtros aplicados não serão exibidos para a seleção “Todos” padrão.

![Filtros aplicados ao Insights do Partner Center](./media/insights-page-filter-two.png)

**Filtros de data do Insights**: Esse filtro se aplica a toda a seção Insights do Marketplace. Os filtros podem incluir intervalos de datas predeterminados ou um intervalo de datas personalizado.

![Filtros de data do Insights do Partner Center](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Resumo do Insights do Marketplace

A seção de resumo do Insights do Marketplace exibe uma contagem de **Visitas à página**, **Planos de ação** e **Visitantes exclusivos** no intervalo de datas selecionado.

### <a name="page-visits"></a>Visitas à página

Esse número representa a contagem de sessões de usuário distintas na página de oferta (página de detalhes do produto) para um intervalo de datas selecionado. O indicador de porcentagem vermelho/verde representa o percentual de crescimento de visitas à página. O gráfico de tendência representa a contagem mês a mês de visitas à página.

### <a name="unique-visitors"></a>Visitantes exclusivos

Esse número representa a contagem de visitantes distinta durante o intervalo de datas selecionado para as ofertas selecionadas no filtro de página. Um visitante que visitou uma ou mais páginas de detalhes do produto será contado como um único visitante.

### <a name="call-to-action"></a>Plano de ação

Esse número representa a contagem de cliques no botão **Plano de Ação** concluídos na página de oferta (página de detalhes do produto). As **chamadas para a ação** são contadas quando os usuários selecionam os botões **obter agora**, **avaliação gratuita**, **contato me**ou **Test Drive** .

![Resumo do plano de ação do Insights do Partner Center](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visitas à página por região geográfica

O calor abaixo exibe a contagem de **Visitas à página**, **Planos de ação** e **Visitantes exclusivos de acordo com o do país do cliente**. As páginas com mais visitas são representadas por cores mais escuras no mapa e as páginas com menos visitas são representadas por cores mais claras no mapa.

![Divisões geográficas do Insights do Partner Center](./media/insights-geography.png)

O mapa de calor inclui os seguintes recursos:

- O mapa de calor tem uma grade suplementar para exibir os detalhes de **Visitas à página**, **Planos de ação** e **Visitantes exclusivos** em um local específico. Você pode ampliar um local específico, se preferir.  
- **As divisões de países/regiões** é a contagem de todos os países/regiões de onde seus clientes relataram visitas à página durante o intervalo de datas selecionado.
- Você pode pesquisar e selecionar um país na grade para ampliar para a localização no mapa. Reverta para a exibição original selecionando **Página inicial** no mapa.

## <a name="page-visits-versus-unique-visitors-trend"></a>Visitas à página versus tendência de visitantes exclusivos

As colunas abaixo representam a contagem de visitas de páginas mensais, que são exibidas no eixo Y (eixo no lado esquerdo do gráfico). A linha de tendência representa a tendência mensal de visitantes exclusivos, que é exibida no eixo Y secundário (eixo no lado direito do gráfico), para suas ofertas publicadas nas vitrines: o Azure Marketplace e o AppSource.

![Visitas à página do Insights do Partner Center versus tendência de visitantes exclusivos](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Plano de ação versus visitantes exclusivos com CTAs

As colunas empilhadas representam planos de ação mensais (CTA), que são divididas por tipos de CTA (**Obter agora**, **Entrar em contato comigo** e **Avaliação gratuita**) e plotadas no eixo Y (eixo no lado esquerdo da página). A linha de tendência representa a tendência mensal de visitantes exclusivos com CTAs, que é exibida no eixo Y secundário (eixo no lado direito do gráfico), para suas ofertas publicadas no Azure Marketplace e no AppSource.

![Plano de ação do Insights do Partner Center versus visitantes exclusivos com CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visitas à página e planos ação por ofertas

O gráfico de pizza externo representa a divisão de **Visitas à página** com base nas ofertas que você publicou no marketplace e selecionadas no filtro. O gráfico interno representa a divisão dos **Planos de ação** para as mesmas ofertas.

![Visitas à página do Insights do Partner Center e planos ação por ofertas](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Tendência percentual do plano de ação

A **Tendência percentual do plano de ação** apresenta o percentual de CTA para as ofertas publicadas no Marketplace. CTA % = (CTAs/visitas à página) * 100.

![Tendência percentual do plano de ação do Insights do Partner Center](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visitas à página e planos de ação por domínios de referência

O gráfico a seguir apresenta os 50 principais domínios de referência. Selecionar um domínio de referência específico mostra a tendência mensal de visitas à página e planos de ação no gráfico à direita.

![Visitas à página do Insights do Partner Center e planos ação por domínios de referência e campanhas](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Insights do Marketplace

Esta tabela fornece uma exibição em forma de lista das visitas à página e dos planos de ação das ofertas selecionadas classificadas por data.

![Tabela de detalhes do Insights do Partner Center](./media/insights-details-page.png)

- Os dados poderão ser extraídos para um arquivo CSV se a contagem dos registros for menor que 1.000.
- Se a contagem de registros for superior a 1.000, os dados exportados são colocados de forma assíncrona em uma página de downloads pelos próximos 30 dias.
- Filtre os dados por nomes de oferta e nomes de campanha para exibir os dados dos quais você está interessado.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no marketplace comercial do Partner Center, confira [Análise para o marketplace comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre os pedidos em um formato gráfico para download, confira [Painel de pedidos na análise do marketplace comercial](./orders-dashboard.md).
- Para as métricas de cobrança de uso e de medição de VM (máquina virtual), confira [Painel de Uso em análises de marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes sobre as ofertas no Azure Marketplace e no AppSource, confira [Painel de classificações e revisões na análise do marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do marketplace comercial e para um dicionário abrangente de termos de dados, confira [Perguntas frequentes e terminologia para análise do marketplace comercial](./faq-terminology.md).
