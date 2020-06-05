---
title: Painel Resumo para análise da Central de Parceiros no Marketplace Comercial
description: Saiba como acessar gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace no painel Resumo na Central de Parceiros.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: c7f1700cb386a197b33427e2ea615de019284d96
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738312"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Painel de resumo na análise do marketplace comercial

Este artigo fornece informações sobre o painel Resumo na Central de Parceiros. Esse painel exibe gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para suas ofertas.

Para acessar o painel Resumo, abra o item **[Analisar Painel](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace Comercial.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, confira [Perguntas frequentes e terminologia para análise de marketplace comercial](./faq-terminology.md).

## <a name="summary-dashboard"></a>Painel de resumo

O painel **Resumo** apresenta uma visão geral com base em cada tipo de oferta. **Insights** mostra informações críticas rapidamente e fornece uma visão ampla da atividade de vendas de suas ofertas. Você pode visualizar esses relatórios usando o painel **Resumo**. Este artigo apresenta mais detalhes sobre cada um dos seguintes elementos:

- [Intervalo de datas](#date-range)
- [Seção Resumo](#summary-section)
- [Clientes por região](#customers-by-geography)
- [Gráficos de tendência de crescimento](#growth-trend-charts)
- [Placar de líderes do cliente](#customer-leaderboard)
- [Tendência da contagem de estações](#seat-count-trend)
- [Tendência de pedidos de SaaS de avaliação gratuita](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel Resumo

As seções a seguir descrevem como usar o painel de resumo e como ler os dados.

### <a name="date-range"></a>Intervalo de datas

Você pode encontrar uma seleção de intervalo de datas no canto superior direito de cada página. Personalize a saída dos grafos da página **Resumo** selecionando um intervalo de datas com base nos últimos 3, 6 ou 12 meses, ou selecionando um intervalo de data personalizado com uma duração máxima de 12 meses. O intervalo de datas padrão é de seis meses.

![Painel Analisar da Central de Parceiros](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Seção Resumo

A seção Resumo exibe uma contagem de todos os pedidos criados, clientes adquiridos e uso relatados durante o intervalo de datas selecionado. O mês atual parcial será excluído do cálculo dessas métricas. Por exemplo:  Se você selecionou o período de 6M, as horas de uso são calculadas para os seis meses anteriores ao mês atual. Se um intervalo de datas personalizado for selecionado, um valor parcial do mês atual será excluído do cálculo.

![Tendências de crescimento no painel Resumo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Ler a seção de resumo

- **Pedidos**: Contagem de todos os pedidos comprados (excluindo pedidos cancelados) de ofertas que você publicou até o momento.
- **Clientes**: Contagem de todos os clientes que compraram suas ofertas e têm pelo menos um pedido não cancelado.
- **Horas normalizadas de uso**: Definidas como as horas normalizadas de uso para contabilizar o número de núcleos da VM ([número de núcleos da VM] x [horas brutas de uso]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- **Horas brutas de uso**: A quantidade de tempo que as VMs estão em execução em termos de horas. O valor percentual próximo ao **total de pedidos**, **total de clientes**, **horas normalizadas de uso**, **horas brutas de uso**, **visitas à página** e **chamadas para ação** representa a quantidade de crescimento do uso no período selecionado ([uso no último mês - uso no primeiro mês]) / uso no primeiro mês). Conforme descrito acima, um valor parcial do mês atual será excluído desta métrica.
- **Tendências de crescimento**: Se você passar o mouse sobre as colunas do gráfico, os gráficos de barras exibirão o valor de cada mês.
- **Triângulo verde apontando para cima**: Indica uma tendência positiva de crescimento.
- **Triângulo vermelho apontando para baixo**: Indica uma tendência negativa de crescimento em relação ao mês anterior.

### <a name="customers-by-geography"></a>Clientes por região

O mapa de calor **Clientes por região** exibe uma contagem de clientes em um mapa do mundo.

![Clientes por região no painel Resumo](./media/summary-customers-by-geography.png)

- Você pode mover o mapa para exibir a localização exata.
- Você pode ampliar em uma localização específica.
- O mapa de calor possui uma grade suplementar para exibir os detalhes da contagem de clientes, contagem de pedidos, horas normalizadas de uso na localização específica.
- Você pode pesquisar e selecionar um país/região na grade para ampliar para a localização no mapa. Reverta à exibição original pressionando o botão **Página Inicial** no mapa.
- Um **novo** cliente comprou uma de suas ofertas pela primeira vez durante o mês no período selecionado.

### <a name="growth-trend-charts"></a>Gráficos de tendência de crescimento

Você pode visualizar as tendências com base no crescimento de seus **pedidos adquiridos** (inclui pedidos cancelados), **clientes adquiridos** (inclui clientes perdidos) e **uso** relatado, que são exibidos mês a mês de acordo com o intervalo de datas selecionado. Você pode analisar essas tendências ainda mais, selecionando os links abaixo do gráfico, que navegam para as respectivas páginas de **pedido**, **uso**, **cliente** ou **Insights do Marketplace**.

Os gráficos de tendências **Visitas à página e chamada para ação** da oferta do marketplace são exibidos no Azure Marketplace e no AppSource em duas guias.

![Visitas a páginas e gráficos de tendência de ações no painel Resumo](./media/summary-page-visits-and-cta.png)

O gráfico de **pedidos por ofertas** organiza seus pedidos de acordo com o nome da Oferta.

O gráfico de pizza **pedidos por canal de vendas** organiza seus pedidos (incluindo pedidos cancelados pelos clientes) durante o intervalo de datas selecionado, por canal de vendas. Canal de vendas é o tipo de contrato de licenciamento usado pelos clientes para adquirir o Azure, que são o CSP (provedor de soluções de nuvem), Enterprise, Enterprise por revendedor, GTM e pré-pago.

Os gráficos de pizza **Uso por ofertas** e **Uso por canal de vendas** apresentam o detalhamento do uso por principais ofertas e canais de vendas, respectivamente. O gráfico de pizza interno representa o uso bruto e o gráfico de pizza externo representa o uso normalizado.

Os gráficos de pizza **pedidos por tipo de licença de marketplace** e **uso por tipo de licença de marketplace** exibem um detalhamento de pedidos e uso por seu respectivo tipo de licença. Os tipos de licença incluem:

- **Cobrado por meio do Azure**: A Microsoft cobra clientes em seu nome quando você opta por vender sua oferta através da Microsoft com esse tipo de licença. Os tipos de pagamento incluem o pagamento por meio de cartão de crédito ou faturamento da empresa.
- **Traga sua licença**: A Microsoft não cobra dos clientes pelo uso com esse tipo de oferta no marketplace. Esse uso está listado como **Obter Agora (Grátis)** no marketplace.
- **Grátis**: A Microsoft não cobra dos clientes pelo uso com esse tipo de oferta no marketplace. Esse uso está listado como **Avaliação gratuita** no marketplace.
- **Microsoft como revendedor**: Representa as ofertas vendidas pelos revendedores da Microsoft como parte do **programa CSP (Provedor de Soluções de Nuvem)** .

### <a name="customer-leaderboard"></a>Placar de líderes do cliente

Os 50 principais clientes com o maior número de pedidos são exibidos em um *placar de líderes*, classificados pela maior contagem de pedidos e porcentagem de pedidos.

- Selecione um cliente para exibir seus detalhes de perfil, pedidos organizados por oferta ou pedidos organizados por tipo de licença e canal de preços do Azure.
- O gráfico de rosca **Ofertas por pedidos** apresenta as quatro principais ofertas (por contagem de pedidos) e as ofertas restantes agrupadas como "Restantes".
- O gráfico de rosca **Uso normalizado por oferta** apresenta as cinco principais ofertas por uso.

> [!NOTE]
> As informações pessoais do cliente serão apresentadas apenas se o cliente tiver fornecido consentimento. Você pode visualizar essas informações se tiver efetuado login com um nível de permissão da função **Proprietário**. Usuários com a função **Colaborador** não poderão exibir essas informações. [Saiba mais sobre as função de usuário e as permissões](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendência da contagem de estações

O gráfico **pedidos por estação/por site** apresenta o detalhamento de todos os pedidos adquiridos de acordo com o modelo de preços. O gráfico de **tendência da contagem de estações** apresenta assentos versus pedidos adquiridos para toda a sua oferta de Software as a Service (SaaS) por assento.

### <a name="free-trials-saas-orders-trend"></a>Tendência de pedidos de SaaS de avaliação gratuita

O gráfico **Tendência de pedidos de SaaS de avaliação gratuita** apresenta a tendência de pedidos de oferta de SaaS de avaliação gratuita com um período de avaliação de 30 dias.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no marketplace comercial da Central de Parceiros, confira [Análise para o mercado comercial na Central de Parceiros](./analytics.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, confira [Painel de Pedidos na análise do marketplace comercial](./orders-dashboard.md).
- Para as métricas de cobrança de uso e de medição de VM (máquina virtual), confira [Painel de Uso em análises de marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, confira [Painel de Cliente em análises de marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, confira [Painel de Downloads em análises do marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes sobre ofertas no Azure Marketplace e AppSource, confira [Painel de classificações e revisões em análises de marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análise de marketplace comercial e para um dicionário abrangente de termos de dados, confira [Perguntas frequentes e terminologia para análise de marketplace comercial](./faq-terminology.md).
