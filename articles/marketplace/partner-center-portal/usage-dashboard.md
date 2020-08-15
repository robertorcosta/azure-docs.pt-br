---
title: Painel de uso no Microsoft Commercial Marketplace Analytics, Azure Marketplace e Microsoft AppSource
description: Saiba como acessar todas as métricas de cobrança limitada e uso de ofertas de VM. Sob o painel de uso no Partner Center, em Marketplace Comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9b8432a54aa90b7d500898b2f6959d075ac89460
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245325"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Painel de uso na análise do marketplace comercial da Microsoft

Este artigo fornece informações sobre o painel de uso no Partner Center. Este painel exibe todas as métricas de cobrança limitada e uso de ofertas de VM em duas guias separadas: Uso de VM e uso de faturamento medido.

Para acessar o painel de uso, abra o painel **[Analisar Painel](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em **Marketplace Comercial**.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, confira [Perguntas frequentes e terminologia para análise de marketplace comercial](./faq-terminology.md).

## <a name="usage-dashboard"></a>Painel de uso

O painel de uso representa as métricas para todos os usos de cobrança limitada e uso de ofertas de VMs (máquinas virtuais). Essas métricas são encontradas em duas guias separadas: Uso de VM e uso de faturamento medido.

Na guia Uso da VM, há representações gráficas dos seguintes itens:

- [Resumo de uso](#usage-summary)
- [Uso por geografia](#usage-by-geography)
- [Uso por ofertas](#usage-by-offers)
- [Tendência de uso por ofertas e planos](#usage-trend-by-offers-and-plans)
- [Uso por tipo de oferta](#usage-by-offer-type)
- [Uso por tamanho da VM](#usage-by-vm-size)
- [Uso por canal de vendas](#usage-by-sales-channel)
- [Dados de uso detalhados](#detailed-usage-data)

A latência máxima entre a geração de eventos de uso e os relatórios no Partner Center é de 48 horas.

### <a name="usage-summary"></a>Resumo de uso

A tabela de resumo de uso exibe as horas de uso do cliente para todas as ofertas adquiridas.

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- Horas de uso bruto são definidas como quanto tempo as VMs estão em execução em termos de horas.
- O valor percentual representa a alteração de crescimento de uso para o intervalo de datas selecionado ([uso do último mês – uso do primeiro mês])/uso do primeiro mês).
- Triângulos verdes apontando para cima indicam uma mudança de crescimento.
- O triângulo vermelho apontando para baixo indica uma mudança de crescimento negativa em relação ao mês anterior.
- Os gráficos de barras pequenas representam os valores mensais. Você pode exibir o valor de cada mês passando o ponteiro do mouse sobre as colunas do gráfico.

### <a name="usage-by-geography"></a>Uso por geografia

O **uso normalizado por** mapa de calor de Geografia exibe horas de uso mapeadas de acordo com o país/região do cliente. A variação de cor de país/região representa a concentração de uso normalizado. Volte para a exibição original pressionando o botão **Página Inicial** no mapa.

### <a name="usage-by-offers"></a>Uso por ofertas

- O gráfico de pizza de **uso normalizado por ofertas** exibe um detalhamento das horas de uso normalizado por ofertas de acordo com o intervalo de datas selecionado. As cinco principais ofertas são exibidas no gráfico, enquanto o restante é agrupado na categoria **Restantes**.
- O gráfico de barras representa uma tendência de crescimento mês a mês para o intervalo de datas selecionado. As colunas de mês representam horas de uso das ofertas com as horas de uso mais alto para o respectivo mês. O gráfico de linhas mostra a tendência de percentual de crescimento plotada no eixo Y secundário.
- Use o controle deslizante na parte superior do gráfico para rolar da direita para a esquerda ao longo do eixo X e/ou se concentrar em pontos de dados específicos.

### <a name="usage-trend-by-offers-and-plans"></a>Tendência de uso por ofertas e planos

Este gráfico exibe a tendência de uso normalizado para os planos selecionados (anteriormente chamados de SKUs) de uma oferta. A placar da oferta exibe as 50 principais ofertas com o uso mais alto, classificadas por horas de uso. O plano placar exibe os principais planos 50 com o uso mais alto para a oferta selecionada.

### <a name="usage-by-offer-type"></a>Uso por tipo de oferta

- O gráfico de pizza de **uso por tipo de oferta** organiza o uso de acordo com o tipo de oferta.
- As ofertas principais são exibidas no gráfico, e o restante das ofertas é agrupado como “Restantes”.
- O gráfico de **tendência** exibe as tendências de crescimento mês a mês. A coluna de mês representa o uso pelos principais tipos de oferta naquele mês.

### <a name="usage-by-vm-size"></a>Uso por tamanho da VM

Este gráfico representa a tendência de uso para tamanhos de VM selecionados (máximo de cinco) de todas as ofertas/planos. O gráfico de colunas é empilhado com as horas de uso dos tamanhos de VM selecionados.

O placar exibe os 50 principais tamanhos de VM com maior uso e classificados por horas de uso.

### <a name="usage-by-sales-channel"></a>Uso por canal de vendas

- O gráfico de pizza uso por canal de vendas organiza o uso de acordo com o canal de vendas
- O canal de vendas superior com uso mais alto é exibido no gráfico, e o restante do canal de vendas é agrupado como “Restantes”.
- A coluna de mês representa o uso pelo canal de vendas principal naquele mês.
- Os recursos deste gráfico são os mesmos que os do gráfico "Uso por ofertas"

### <a name="detailed-usage-data"></a>Dados de uso detalhados

A **tabela detalhes de uso** exibe uma lista numerada dos 1000 principais registros de uso classificados por uso.

- Cada coluna na grade é classificável.
- Os dados poderão ser extraídos para um arquivo CSV se a contagem dos registros for menor que 1000.
- Se a contagem de registros for superior a 1000, os dados de exportação serão colocados de forma assíncrona em uma página de downloads que ficará disponível para os próximos 30 dias.
- Aplique filtros a **dados de uso detalhados** para exibir somente os dados nos quais você está interessado. Filtrar dados por país/região, canal de vendas, tipo de licença do Marketplace, tipo de uso, nome da oferta, tipo de oferta, avaliações gratuitas, ID de assinatura do Marketplace, ID do cliente e nome da empresa.

> [!NOTE]
> Selecione o **tipo de uso** no filtro de página para exibir gráficos na página na "Exibição normalizada" ou "Exibição bruta". A exibição padrão para esses gráficos é "exibição normalizada".

Os **filtros de página de uso** são aplicados no nível de página. É possível selecionar vários filtros para renderizar o gráfico para os critérios que você escolher e os dados que deseja exibir na grade/exportação de "dados de uso detalhados". Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página pedidos.

- Os **tipos de oferta** e os **nomes de oferta** são listados apenas para as ofertas que você adquiriu durante o intervalo de datas selecionado. Os nomes de oferta da lista são exibidos para os tipos de oferta selecionados na lista.
- A seleção padrão é "Todos" para cada uma das opções de filtro, exceto para **tipo de uso**. A seleção padrão para **tipo de uso** é o uso normalizado. Para exibir o uso bruto nos gráficos, selecione "uso bruto".
- Os filtros aplicados mostram a seleção de contagem para as seleções de filtro feitas. Os filtros aplicados não são exibidos para as seleções padrão.

> [!NOTE]
> Veja uma definição detalhada de cada um dos campos na grade "dados de pedido detalhados", filtros de página e todas as seleções possíveis na seção do dicionário de dados do artigo [Perguntas frequentes e terminologia](link needed).

A guia **Uso de cobrança limitada** apresenta informações de uso para os tipos de oferta em que o uso é limitado por dimensão de medidor. O tipo de oferta de SaaS excedente é apresentado no momento. A guia apresenta representações gráficas de tendências excedentes para o uso de cobrança limitada de SaaS:

- **Tendência excedente por dimensão de medidor**: Exibe a tendência excedente mensal para a dimensão de medidor selecionada de uma oferta. O eixo X representa o mês e o eixo Y representa a quantidade de uso. A unidade de medida do medidor personalizado também é exibida no eixo Y.
- **Tendência de excedente por plano**: representa a tendência de quantidade de uso da dimensão de medidor selecionada por planos. Os planos exibidos representarão os cinco principais planos com a maior quantidade de uso para a oferta selecionada.
- **Tendência excedente pelos 50 principais clientes**: As 50 principais ofertas com as horas de uso mais altas são exibidas em um ***placar*** e são classificadas pelo uso mais alto do medidor personalizado. Selecione um cliente no placar para exibir a tendência de uso de uma dimensão de medidor selecionada.
- **Tendência excedente pelos principais clientes**: Apresenta os principais percentuais do cliente que contribuem para o percentual do uso geral. O principal percentual do cliente é exibido ao longo do eixo X e é determinado pela quantidade de uso do cliente. O eixo Y mostra a quantidade de uso. Você pode exibir detalhes passando o ponteiro do mouse sobre pontos ao longo do gráfico de linhas.

Se você tiver várias ofertas que usam medidores personalizados, o relatório uso de cobrança medido mostrará informações de uso para todas as suas ofertas, de acordo com suas dimensões de medidor personalizadas.

> [!NOTE]
> Os detalhes de uso e todos os gráficos nessa página são exibidos para qualquer dimensão de medidor selecionada para o filtro de página.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no marketplace comercial da Central de Parceiros, confira [Análise para o mercado comercial na Central de Parceiros](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, confira [Painel de Pedidos na análise do marketplace comercial](./orders-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes sobre ofertas no Microsoft AppSource e no Azure Marketplace, confira [Painel de classificações e revisões em análises de marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análise de marketplace comercial e para um dicionário abrangente de termos de dados, confira [Perguntas frequentes e terminologia para análise de marketplace comercial](./faq-terminology.md).
