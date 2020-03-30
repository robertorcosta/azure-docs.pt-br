---
title: Painel de resumo para análises do Partner Center no Mercado Comercial
description: Saiba como acessar gráficos, tendências e valores de dados agregados que resumem a atividade do mercado no painel Resumo no Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3da3109bd813fc3b99a4f59e5a357fa351c75394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281367"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Painel de resumo em análises de mercado comercial

Este artigo fornece informações sobre o painel Resumo no Partner Center. Este painel exibe gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para suas ofertas.

Para acessar o painel Resumo, abra o **[painel Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Comercial Marketplace.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, consulte [perguntas freqüentes e terminologia para análises de mercado comercial](./faq-terminology.md).

## <a name="summary-dashboard"></a>Painel de resumo

O **painel Resumo** apresenta uma visão geral com base em cada tipo de oferta. **Os insights** mostram informações críticas rapidamente e fornecem uma visão ampla da atividade de vendas de suas ofertas. Você pode visualizar esses relatórios usando o painel **Resumo.** Este artigo entra em mais detalhes sobre cada um dos seguintes elementos:

- [Intervalo de datas](#date-range)
- [Seção resumo](#summary-section)
- [Clientes por geografia](#customers-by-geography)
- [Gráficos de tendência de crescimento](#growth-trend-charts)
- [Tabela de classificação de clientes](#customer-leaderboard)
- [Tendência de contagem de assentos](#seat-count-trend)
- [Testes gratuitos SaaS pedidos tendência](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel Resumo

As seções a seguir descrevem como usar o painel de resumo e como ler os dados.

### <a name="date-range"></a>Intervalo de datas

Você pode encontrar uma seleção de faixa de data no canto superior direito de cada página. Personalize a saída dos gráficos da página **Resumo** selecionando um intervalo de datas com base nos últimos 3, 6 ou 12 meses ou selecionando um intervalo de datas personalizado com uma duração máxima de 12 meses. O intervalo de data padrão é de seis meses.

![Painel de análise do Partner Center Analyze](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Seção resumo

A seção Resumo exibe uma contagem de todos os pedidos criados, clientes adquiridos e uso relatados durante a faixa de data selecionada. O mês atual parcial será excluído do cálculo dessas métricas. Por exemplo: Se você selecionou o período de 6M, as horas de uso serão calculadas para os seis meses anteriores ao mês atual. Se uma faixa de data personalizada for selecionada, um valor parcial do mês atual será excluído do cálculo.

![Tendências de crescimento no painel Resumo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lendo a seção resumo

- **Ordens**: Contagem de todas as ordens compradas (exclui pedidos cancelados) para ofertas que você publicou até agora.
- **Clientes**: Contagem de todos os clientes que compraram suas ofertas e têm pelo menos um pedido não cancelado.
- **Horas de uso normalizadas**: Definidas como as horas de uso normalizadas para contabilizar o número de núcleos VM ([número de núcleos VM] x [horas de uso bruto]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos VM].
- **Horas de uso bruto**: A quantidade de tempo que as VMs têm sido executados em termos de horas. O valor percentual próximo ao **total de pedidos,** **total de clientes,** **horas de uso normalizadas,** **horas de uso bruto,** visitas de **página**e chamadas **para ações** representam a quantidade de crescimento de uso para a faixa de data selecionada ([uso do mês passado – uso do primeiro mês])/ uso do primeiro mês). Conforme descrito acima, uma quantidade parcial do mês atual será excluída desta métrica.
- **Tendências de crescimento**: Se você passar o tempo sobre as colunas do gráfico, os gráficos de barras exibirão o valor de cada mês.
- **Triângulo verde apontando para cima**: Indica uma tendência positiva de crescimento.
- **Triângulo vermelho apontando para baixo**: Indica uma tendência de crescimento negativo em relação ao mês anterior.

### <a name="customers-by-geography"></a>Clientes por geografia

O mapa de calor **Clientes por geografia** exibe uma contagem de clientes em um mapa-múndi.

![Clientes por geografia no painel Resumo](./media/summary-customers-by-geography.png)

- Você pode mover o mapa para ver a localização exata.
- Você pode ampliar para um local específico.
- O mapa de calor tem uma grade suplementar para visualizar os detalhes da contagem de clientes, contagem de pedidos, horas de uso normalizadas no local específico.
- Você pode pesquisar e selecionar um país na grade para ampliar o local no mapa. Reverter para a visualização original pressionando o botão **Home** no mapa.
- Um **novo** cliente adquiriu uma de suas ofertas pela primeira vez durante o mês dentro da faixa de data selecionada.

### <a name="growth-trend-charts"></a>Gráficos de tendência de crescimento

Você pode visualizar tendências com base no crescimento de seus **pedidos comprados** (inclui pedidos cancelados), **clientes adquiridos** (inclui clientes perdidos) e **uso** relatado, que são exibidos mês a mês de acordo com a faixa de data selecionada. Você pode analisar ainda mais essas tendências selecionando links abaixo do gráfico, que navegam para as respectivas páginas **de ordem,** **uso,** **cliente**ou **Marketplace Insights.**

As visitas de página do Marketplace e os gráficos de **tendências** de chamada para ação são exibidos para o mercado Azure e o AppSource em duas guias.

![Visitas de página e call to actions trend charts no painel Resumo](./media/summary-page-visits-and-cta.png)

O **gráfico de pedidos por ofertas** organiza seus pedidos de acordo com o nome da Oferta.

Os pedidos pelo gráfico de tortas **do canal de vendas** organizam seus pedidos (incluindo pedidos que os clientes cancelaram) durante a faixa de data selecionada, pelo canal Vendas. Canal de vendas é o tipo de contrato de licenciamento usado pelos clientes para comprar o Azure, que são Cloud Solution Provider (CSP), Enterprise, Enterprise through Reseller, GTM e Pay As You Go.

**O uso por ofertas** e **uso por gráficos de tortas de canais** de vendas apresentam a divisão do uso pelas principais ofertas e canais de vendas, respectivamente. O gráfico interno da torta representa o uso bruto e o gráfico de tortas externas representa o uso normalizado.

Os **pedidos por tipo de licença de marketplace** e uso por gráficos de pie tipo de licença de **marketplace** exibem uma divisão de pedidos e uso por seu respectivo tipo de licença. Os tipos de licença incluem:

- **Faturado através do Azure**: A Microsoft fatura clientes em seu nome quando você optar por vender sua oferta através da Microsoft com este tipo de licença. Os tipos de pagamento incluem pagamento à medida que você vai via cartão de crédito ou faturamento da Enterprise.
- **Traga sua própria licença**: a Microsoft não fatura clientes por seu uso com este tipo de oferta de marketplace. Este uso está listado como **Get it now (Grátis)** no mercado.
- **Grátis**: A Microsoft não fatura clientes por seu uso com este tipo de oferta de marketplace. Este uso é listado como **teste gratuito** no mercado.
- **Microsoft como revendedor**: Representa ofertas vendidas por revendedores da Microsoft como parte do **programa Cloud Solution Provider (CSP).**

### <a name="customer-leaderboard"></a>Tabela de classificação de clientes

Os 50 maiores clientes com o maior número de pedidos são exibidos em uma *placa de líder,* classificada pela maior contagem de pedidos e porcentagem de pedidos.

- Selecione um cliente para visualizar seus detalhes de perfil, pedidos organizados por oferta ou pedidos organizados pelo tipo de licença do Azure e canal de preços.
- O gráfico **Ofertas por pedidos** apresenta as quatro melhores ofertas (por contagem de pedidos) e as demais ofertas agrupadas como 'Rest All'.
- O **uso normalizado por oferta** de gráfico de donuts apresenta as cinco principais ofertas por uso.

> [!NOTE]
> As informações pessoais do cliente serão apresentadas somente se o cliente tiver dado o consentimento. Você pode visualizar essas informações se tiver logado com um nível de permissões de função **Proprietário.** Os usuários com a função **Contribuinte** não poderão visualizar essas informações. [Saiba mais sobre funções e permissões do usuário.](./manage-account.md#define-user-roles-and-permissions)

### <a name="seat-count-trend"></a>Tendência de contagem de assentos

Os **pedidos por assento/ por** gráfico do site apresentam a divisão de todas as ordens compradas de acordo com o modelo de preços. O gráfico **de tendência seleção de assentos** apresenta assentos versus pedidos comprados para todas as ofertas de Software as a Service (SaaS) por assento.

### <a name="free-trials-saas-orders-trend"></a>Testes gratuitos SaaS pedidos tendência

O **gráfico de tendência de pedidos de pedidos saaS** de teste gratuito apresenta a tendência de pedidos para testes gratuitos que a SaaS oferece com um período de teste de 30 dias.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, consulte [Orders Dashboard em análises de mercado comercial](./orders-dashboard.md).
- Para a Máquina Virtual (VM) oferece métricas de uso e faturamento medido, consulte [O Painel de Uso em análises de mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análises de Mercado Comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O Painel de Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análises de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas e terminologias freqüentes para análises de mercado comercial](./faq-terminology.md).
