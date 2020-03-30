---
title: Painel de pedidos do Partner Center orders em análises de mercado comercial
description: Saiba como acessar relatórios analíticos sobre seus pedidos de oferta de marketplace em um formato gráfico e para download.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d0db6553a774a69bb8a55538cbd2b4a333be9316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281401"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Painel de pedidos em análises de mercado comercial

Este artigo fornece informações sobre o **painel De ordens** no Partner Center. Este painel exibe informações sobre seus pedidos em um formato gráfico e para download.

Para acessar o **painel de pedidos** nas ferramentas de análise do Partner Center, abra o painel **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Mercado Comercial.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, consulte [perguntas freqüentes e terminologia para análises de mercado comercial](./faq-terminology.md).

## <a name="orders-dashboard"></a>Painel de pedidos

O **painel Pedidos do** menu **Analisar** exibe os pedidos atuais de todas as suas ofertas SaaS. Você pode visualizar representações gráficas dos seguintes itens:

- [Resumo do pedido](#order-summary)
- [Ordens por geografia](#orders-by-geography)
- [Pedidos por ofertas](#orders-by-offers)
- [Tendência de pedidos por site versus por assento](#orders-trend-per-site-versus-per-seat)
- [Pedidos por SKUs](#orders-by-skus)
- [Tendência de pedidos e assentos](#orders-and-seats-trend)
- [Tabela de detalhes do pedido](#order-details-table)

> [!NOTE]
> Há diferenças entre a forma como os relatórios de análise são exibidos no Portal de Parceiros em Nuvem (CPP) e o novo programa de Mercado Comercial no Partner Center. Uma maneira específica é que o Seller Insights no CPP tenha uma guia **Orders & Usage,** que exibe dados para ofertas baseadas em uso e **ofertas baseadas** em uso. No Partner Center, a página **Pedidos** tem uma guia separada para Ofertas SaaS.

## <a name="order-dashboard-details"></a>Detalhes do painel de pedidos

Esta seção descreve os relatórios de análise com mais detalhes.

### <a name="order-summary"></a>Resumo do pedido

A seção Resumo da Ordem exibe uma contagem de todos os pedidos comprados (excluindo pedidos cancelados), pedidos cancelados e assentos.

O valor percentual ao lado do Total de Pedidos representa a quantidade de crescimento da faixa de data selecionada.

![Centro de Parceiros Analisar resumo do pedido](./media/order-summary.png)

- Um triângulo verde apontando para cima indica uma tendência positiva de crescimento.
- Um triângulo vermelho apontando para baixo indica uma tendência de crescimento negativo em relação ao mês anterior.
- As tendências de crescimento são representadas por gráficos de micro barras. Você pode exibir o valor para cada mês pairando sobre as colunas dentro do gráfico.
- Os pedidos cancelados são uma contagem de pedidos que foram comprados anteriormente e depois cancelados durante o intervalo de datas selecionado.
- Os assentos são uma contagem de assentos criados durante a faixa de data selecionada.

### <a name="orders-by-geography"></a>Ordens por geografia

O mapa de calor **Orders by geografia** exibe uma contagem de seus pedidos em um mapa do mundo e mostra assentos mapeados com base no País do Cliente. Este mapa de calor funciona da mesma forma que o **[Cliente por mapa de calor geografia](./customer-dashboard.md#customer-by-geography)**.

![Centro de Parceiros Analisar pedidos por geografia](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Pedidos por ofertas

O **Gráfico de Pedidos por Ofertas** organiza pedidos (incluindo pedidos cancelados) de acordo com seus nomes de oferta.

- As melhores ofertas são exibidas no gráfico e o resto das ofertas são agrupadas como 'Rest All'.
- Você pode selecionar ofertas específicas na legenda para exibir apenas as ofertas no gráfico.
- Pairando sobre uma fatia no gráfico exibirá o número de pedidos e a porcentagem dessa oferta em comparação com o número total de pedidos em todas as ofertas.
- A **tendência de pedidos por ofertas** mostra tendências de crescimento mês a mês. A coluna do mês representa o número de pedidos por nome de oferta. O gráfico de linhas mostra a tendência de crescimento plotada em um eixo z.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda ao longo do eixo x e focar em pontos de dados específicos.
- Você pode exibir o gráfico de tendências selecionando um item específico na legenda.
- Você também pode optar por exibir tendências e dados para **pedidos cancelados**. O gráfico funcionará da mesma forma que as **ordens por ofertas gráficas.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendência de pedidos por site versus por assento

O **gráfico por site versus por assento** donut representa a divisão de pedidos de SaaS por site e por assento saaS comprados pelos clientes (este gráfico inclui pedidos cancelados). O gráfico da coluna representa a tendência do site SaaS e por assento de pedidos SaaS comprados pelos clientes (este gráfico inclui pedidos cancelados).

### <a name="orders-by-skus"></a>Pedidos por SKUs

O gráfico **Orders by SKUs** representa a tendência de pedidos no nível da Unidade de Manutenção de Ações (SKU) para todas as suas ofertas (isso inclui pedidos cancelados). O gráfico de donuts representa a divisão das cinco principais ordens SKU e o gráfico de colunas representa a tendência de pedidos para as cinco Principais SKUs.

### <a name="orders-and-seats-trend"></a>Tendência de Pedidos e Assentos

O gráfico **de tendência de pedidos e assentos** apresenta as 50 melhores ofertas com o maior número de pedidos. Estes são exibidos em uma placa de lídere e são classificados pela maior contagem de pedidos e porcentagem de pedidos.

- **Pedidos por SKUs**: Selecione uma oferta para visualizar a contagem de pedidos para as cinco Principais SKUs no gráfico.
- **Assentos por SKUs**: A tendência mensal de assentos para as cinco Principais SKUs. Se a oferta selecionada não for uma oferta por assento, você não verá nenhum dado neste gráfico de área.

### <a name="canceled-orders-by-offers"></a>Pedidos cancelados por ofertas

Os **pedidos cancelados por ofertas** de gráfico de tortas organizam todos os seus pedidos cancelados de acordo com seus nomes de oferta. As melhores ofertas são exibidas no gráfico e o resto das ofertas são agrupadas como "Rest All". Você pode selecionar ofertas específicas na legenda para exibir no gráfico.

- Pairando sobre uma fatia no gráfico exibirá o número de pedidos e a porcentagem da oferta selecionada em comparação com o número total de pedidos em todas as ofertas.
- O gráfico da coluna mostra as tendências mês a mês. As colunas representam o número de pedidos cancelados pelo nome da oferta. Você pode usar o controle deslizante no topo do gráfico para rolar para a direita e para a esquerda ao longo do eixo x e focar em pontos de dados específicos. Você pode exibir o gráfico de tendências selecionando um item específico na legenda.

### <a name="order-details-table"></a>Tabela de detalhes do pedido

A tabela de detalhes da Ordem exibe uma lista numerada das 1000 principais ordens classificadas por data de aquisição.

- Cada coluna na grade é classificada.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros for superior a 1000, os dados exportados serão colocados assíncronamente em uma página de downloads pelos próximos 30 dias.
- Os filtros podem ser aplicados na **tabela de detalhes do Pedido** para exibir apenas os dados que você está interessado. Os dados podem ser filtrados por Country, tipo de licença do Azure, tipo de licença do Marketplace, tipo de oferta, status do pedido, trilhas grátis, ID de assinatura do Marketplace, ID do cliente e nome da empresa.

#### <a name="orders-page-filters"></a>Filtros de página de pedidos

Esses filtros são aplicados no nível da página.

Você pode selecionar vários filtros para renderizar o gráfico para os critérios que você escolher para exibir e os dados que deseja exibidos na grade/exportação **de dados de ordem detalhada.** Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página de pedidos.

- Os tipos de oferta suse nomes de ofertas são listados apenas para ofertas que você tem pedidos durante a faixa de data selecionada. Os nomes das ofertas na lista são exibidos para os tipos de ofertas que você selecionou na lista.
- Os filtros aplicados mostram as métricas totais dentro de cada seleção para cada filtro selecionado. Os filtros aplicados não são exibidos quando a seleção padrão é escolhida.
- Se **All** estiver selecionado para uma das listas de saque, todas as métricas da página selecionada serão agregadas. Por exemplo: "Todos" na opção de filtro tipos de oferta significa que todos os tipos de oferta foram selecionados. Esta é a seleção padrão para as listas de saque. Os monitores de filtros aplicados não mostrarão nada quando **Tudo** estiver selecionado.
- **Seleção de múltiplos valores**: Todas as métricas da página serão agregadas para todas as seleções feitas a lista de isento. Se várias seleções forem feitas, o filtro aplicado mostrará a contagem de todas as seleções feitas. Veja a imagem abaixo para referência.

    ![Ordem de análise do Partner Center com múltiplos valores aplicados ao filtro](./media/filters-applied.png)

- **Seleção de valor único**: Se um valor for selecionado, o filtro aplicado mostrará a contagem do único filtro selecionado. Veja abaixo a imagem para referência.

     ![Ordem de análise do Partner Center com valor único aplicado ao filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para sua oferta, consulte [O painel Resumo em análises de Mercado Comercial](./summary-dashboard.md).
- Para a Máquina Virtual (VM) oferece métricas de uso e faturamento medido, consulte [O painel de uso em análises de mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [o painel do cliente em análises de mercado comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O painel Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análises de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas e terminologias freqüentes para análises de mercado comercial](./faq-terminology.md).
