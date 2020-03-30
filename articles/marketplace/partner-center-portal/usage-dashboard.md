---
title: Painel de uso em análises de mercado comercial no Partner Center
description: Saiba como acessar todas as ofertas de VM de uso e métricas de faturamento medidos.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0cd6614c1ccc51a2b25f115dccf0ee389075aa70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285122"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Painel de uso em análises de mercado comercial

Este artigo fornece informações sobre o painel de uso no Partner Center. Este painel exibe todas as métricas de faturamento da VM em duas abas separadas: uso de VM e uso de faturamento medido.

Para acessar o painel de uso, abra o painel **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Comercial Marketplace.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, consulte [perguntas freqüentes e terminologia para análises de mercado comercial](./faq-terminology.md).

## <a name="usage-dashboard"></a>Painel de uso

O Painel de Uso representa as métricas para todas as Máquinas Virtuais (VM) que oferecem uso e uso de faturamento medido. Estes são encontrados em duas guias separadas: uso de VM e uso de faturamento medido.

Na guia de uso da VM, há representações gráficas dos seguintes itens:

- [Resumo de uso](#usage-summary)
- [Uso por geografia](#usage-by-geography)
- [Uso por ofertas](#usage-by-offers)
- [Tendência de uso por ofertas e SKUs](#usage-trend-by-offers-and-skus)
- [Uso por tipo de oferta](#usage-by-offer-type)
- [Uso por tamanho VM](#usage-by-vm-size)
- [Uso por canal de vendas](#usage-by-sales-channel)
- [Dados de uso detalhados](#detailed-usage-data)

> [!NOTE]
> Os relatórios de análise são exibidos de forma diferente no Cloud Partner Portal (CPP) e no Partner Center. **Os insights do vendedor** no CPP têm uma guia de pedidos e uso, que exibe dados tanto para ofertas baseadas em uso quanto para ofertas baseadas em uso. No Partner Center, as métricas de uso são exibidas em uma página separada.

### <a name="usage-summary"></a>Resumo de uso

A tabela de resumo de uso exibe as horas de uso do cliente para todas as ofertas que eles compraram.

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos VM ([número de núcleos VM] x [horas de uso bruto]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos VM].
- As horas de uso bruto são definidas como a quantidade de tempo que as VMs têm executado em termos de horas.
- O valor percentual representa a mudança de crescimento de uso para a faixa de data selecionada ([uso do mês passado – uso do primeiro mês])/ uso do primeiro mês).
- Triângulos verdes apontando para cima indicam uma mudança de crescimento.
- Triângulo vermelho apontando para baixo indica uma variação negativa de crescimento em relação ao mês anterior.
- Os gráficos de micro barras representam valores mensais. Você pode exibir o valor para cada mês pairando sobre as colunas dentro do gráfico.

### <a name="usage-by-geography"></a>Uso por geografia

O **uso normalizado pelo** mapa de calor da geografia exibe horas de uso mapeadas de acordo com o país cliente. A variação de cor do país representa a concentração de uso normalizada. Reverter para a visualização original pressionando o botão **home** no mapa.

### <a name="usage-by-offers"></a>Uso por ofertas

- O **uso normalizado por ofertas** gráfico de torta exibe uma divisão das horas de uso normalizadas por ofertas de acordo com a faixa de data selecionada. As 5 melhores ofertas são exibidas em gráfico, enquanto as demais são agrupadas na categoria "rest all".
- O gráfico de barras mostra uma tendência de crescimento mês a mês para a faixa de data selecionada. As colunas do mês representam horas de uso das ofertas com as maiores horas de uso para o respectivo mês. O gráfico de linha mostra a tendência percentual de crescimento traçada no eixo Y secundário.
- Use o controle deslizante na parte superior do gráfico para rolar da direita para a esquerda ao longo do eixo x e/ou focar em pontos de dados específicos.

### <a name="usage-trend-by-offers-and-skus"></a>Tendência de uso por ofertas e SKUs

Este gráfico exibe a tendência de uso normalizado para as SKUs selecionadas de uma oferta. O ranking de ofertaexibe as 50 melhores ofertas com o maior uso e são classificadas por horas de uso. A tabela de classificação SKU exibe as 50 SKUs mais altas com o maior uso para a oferta selecionada.

### <a name="usage-by-offer-type"></a>Uso por tipo de oferta

- O **uso por gráfico de** torta tipo de oferta organiza o uso de acordo com o tipo de oferta.
- As principais ofertas são exibidas no gráfico e o resto das ofertas são agrupadas como 'Rest All'.
- O gráfico **de tendências** mostra tendências de crescimento mês a mês. A coluna do mês representa o uso pelos principais tipos de ofertas naquele mês.

### <a name="usage-by-vm-size"></a>Uso por tamanho VM

Este gráfico representa a tendência de uso para tamanhos VM selecionados (max 5) de todas as suas ofertas/SKUs. O gráfico da coluna é empilhado com as horas de uso dos tamanhos vm selecionados.

A tabela de classificação exibe os 50 principais tamanhos de VM com maior uso e classificados por horas de uso.

### <a name="usage-by-sales-channel"></a>Uso por canal de vendas

- O uso por gráfico de tortas de canal de vendas organiza o uso de acordo com o canal de vendas
- O canal de vendas mais alto com maior uso é exibido no gráfico e o resto do canal de vendas é agrupado como 'Rest All'.
- A coluna do mês representa o uso pelo canal de vendas mais alto naquele mês.
- As características deste gráfico são as mesmas do gráfico 'Uso por Ofertas'

### <a name="detailed-usage-data"></a>Dados de uso detalhados

A **tabela de detalhes de uso** exibe uma lista numerada dos 1000 principais registros de uso classificados pelo uso.

- Cada coluna na grade é classificada.
- Os dados podem ser extraídos para um arquivo CSV se a contagem dos registros for menor que 1000.
- Se a contagem de registros for superior a 1000, os dados de exportação serão colocados assíncronamente em uma página de downloads que estará disponível pelos próximos 30 dias.
- Os filtros podem ser aplicados aos **dados de uso detalhados** para exibir apenas os dados que você está interessado. Os dados podem ser filtrados por país, canal de vendas, tipo de licença do Marketplace, tipo de uso, nome de oferta, tipo de oferta, testes gratuitos, ID de assinatura do Marketplace, ID do cliente e nome da empresa.

> [!NOTE]
> Selecione o **tipo de uso** no filtro de página para exibir gráficos na página em "Exibição normalizada" ou "Exibição bruta". A exibição padrão desses gráficos é "Exibição normalizada".

Os **filtros de página de uso** são aplicados no nível da página. Você pode selecionar vários filtros para renderizar o gráfico para os critérios que você escolher para exibir e os dados que deseja exibidos na grade/exportação de "dados de uso detalhados". Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página de pedidos.

- **Os tipos de** oferta e **os nomes da oferta** são listados apenas para as ofertas que você adquiriu durante a faixa de data selecionada. Os nomes da oferta na lista são exibidos para tipos de oferta selecionados na lista.
- A seleção padrão é "Todos" para cada uma das opções de filtro, exceto para **o tipo de uso**. A seleção padrão para **o tipo de uso** é normalizada. Para exibir o uso bruto nos gráficos, selecione "uso bruto".
- Os filtros aplicados mostram as seleções de contagem para as seleções de filtros que foram feitas. Os filtros aplicados não são exibidos para as seleções padrão.

> [!NOTE]
> Uma definição detalhada de cada um dos campos na grade de "dados de ordem detalhada", filtros de página e todas as seleções possíveis são definidas na seção de dicionário de dados do artigo [FAQs e terminologia.](link needed)

A guia **de uso de faturamento medido** apresenta informações de uso para tipos de oferta saem medidos por dimensão do medidor. O excesso de oferta saaS é apresentado atualmente. A guia apresenta representações gráficas de tendências de excesso de idade para o uso de faturamento medido do SaaS:

- **Tendência de sobreage por dimensão do medidor**: Exibe a tendência mensal de sobrefrequência para a dimensão do medidor selecionado de uma oferta. O Eixo X representa o mês e o Eixo Y representa a quantidade de uso. A unidade de medição do medidor personalizado também é exibida no Eixo Y.
- **Tendência de excesso por SKU**: Representa a tendência de quantidade de uso da dimensão do medidor selecionado por SKUs. As SKUs exibidas representarão as 5 Melhores SKUs com a maior quantidade de uso para a oferta selecionada.
- **Tendência de overage pelos Top 50 Clientes**: As 50 ofertas mais altas com as maiores horas de uso são exibidas em uma ***placa líder*** e são classificadas pelo uso mais alto do medidor personalizado. Selecione um cliente na tabela de classificação para visualizar a tendência de uso de uma dimensão de medidor selecionada.
- **Tendência de excesso de clientes :** Apresenta percentil(s) de alto número de clientes que contribuem para o % do uso global. O percentil superior do cliente é exibido ao longo do eixo X e é determinado pela quantidade de uso do cliente. O eixo Y exibe a quantidade de uso. Você pode exibir detalhes pairando sobre pontos ao longo do gráfico de linha.

> [!NOTE]
> Os detalhes de uso e todos os gráficos nesta página são exibidos para qualquer dimensão do medidor selecionado para o filtro da página.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para obter gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para sua oferta, consulte [Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, consulte [Orders Dashboard em análises de mercado comercial](./orders-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análises de Mercado Comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O Painel de Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análises de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas e terminologias freqüentes para análises de mercado comercial](./faq-terminology.md).
