---
title: Painel de cliente na análise do marketplace comercial da Microsoft no Partner Center
description: Aprenda a acessar informações sobre os clientes, incluindo tendências de crescimento, usando o painel de cliente na análise do marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 9f71a668b3473521e9d8d7c9318ab2e557380845
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121751"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Painel do cliente na análise do marketplace comercial

Este artigo fornece informações sobre o **painel de cliente** no Partner Center. Esse painel exibe informações sobre os clientes, incluindo tendências de crescimento, apresentadas em um formato gráfico para download.

Para acessar o **painel de cliente**, abra a painel **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

>[!NOTE]
> Para obter definições detalhadas da terminologia de análise, confira [Perguntas frequentes e terminologia para análise do marketplace comercial](./faq-terminology.md).

## <a name="customer-dashboard"></a>Painel de cliente

O **painel de cliente** do menu **Analisar** exibe dados para clientes que adquiriram as ofertas. Você pode ver as representações gráficas dos seguintes itens:

- [Resumo do cliente](#customer-summary)
- [Cliente por região geográfica](#customer-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por pedidos e uso](#customers-by-orders-and-usage)
- [Clientes por SKUs](#customers-by-skus)
- [Pedidos e uso por tipo de cliente](#orders-and-usage-by-customer-type)
- [Tabela de detalhes do cliente](#customer-details-table)
- [Filtros de página do cliente](#customer-page-filters)

### <a name="customer-summary"></a>Resumo do cliente

A seção Resumo do cliente exibe uma contagem de todos os clientes, incluindo novos, existentes e rotativos, durante o intervalo de datas selecionado.

- O total de clientes é definido como a contagem de todos os clientes que adquiriram a oferta e têm pelo menos um pedido que não foi cancelado.
- O percentual de crescimento dos clientes em comparação ao mês anterior é indicado pelo número e pelo indicador de alta em verde ou pelo indicador de baixa em vermelho.
- As tendências de crescimento são representadas por gráficos de barras e exibem o valor de cada mês ao passar o mouse sobre as colunas do gráfico.

Há três **tipos de clientes**: novos, existentes e rotativos.

- Um cliente novo adquiriu uma ou mais ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais ofertas antes do mês selecionado.
- Um cliente rotativo cancelou todas as ofertas adquiridas anteriormente.

### <a name="customer-by-geography"></a>Cliente por região geográfica

O gráfico de **Cliente por região geográfica** mostra as contagens de todos os clientes e os clientes são adquiridos durante o intervalo de datas selecionado e mapeados com base no país/região do cliente. A cor de clara a escura no mapa representa o valor de baixo a alto da contagem de clientes. Clique em um registro na tabela para ampliar um país/região.

O mapa de calor exibe a contagem de clientes e o percentual por país/região do cliente. Você pode mover o mapa para exibir o local exato e ampliar um local específico. Esse mapa tem uma grade complementar que permite exibir o percentual de clientes por local, bem como os clientes adicionados recentemente a esse local.

### <a name="customer-trends"></a>Tendências do cliente

O gráfico de **Tendências do cliente** exibe uma contagem de todos os clientes, incluindo novos, existentes e rotativos, com uma tendência de crescimento mensal.

- O gráfico de linhas representa as porcentagens de crescimento geral do cliente.
- A coluna Mês representa a contagem de clientes empilhados por clientes novos, existentes e rotativos.
- A contagem de clientes rotativos é exibida na direção negativa do eixo Y.
- Você pode selecionar itens de legenda específicos para obter exibições mais detalhadas. Por exemplo, selecione clientes novos na legenda para exibir apenas os clientes novos.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda no eixo x e concentrar-se em pontos de dados específicos para exibir mais detalhadamente.
- Passar o mouse sobre uma coluna do gráfico exibe os detalhes somente para esse mês.

### <a name="customers-by-orders-and-usage"></a>Clientes por pedidos e uso

O gráfico **Clientes por pedidos/uso** tem três guias, "pedidos", "uso normalizado" e "uso bruto". O **principal percentual do cliente** é exibido ao longo do eixo X, conforme determinado pelo número de pedidos. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (grafo de linhas) exibe a porcentagem cumulativa do número total de pedidos. Você pode exibir os detalhes passando o mouse sobre os pontos ao longo do gráfico de linhas.

Por exemplo, confira o gráfico abaixo sobre o uso normalizado: O 30º principal percentual de clientes está contribuindo para 87% do uso normalizado de modo cumulativo. O 30º percentual de clientes está contribuindo apenas para 1.570.000 de horas de uso.

### <a name="customers-by-skus"></a>Clientes por SKUs

Os gráficos de **Clientes por SKUs/uso** são descritos abaixo.

1. O painel Líder apresenta os detalhes dos 50 principais clientes classificados por contagem de pedidos. Depois de selecionar um cliente, os detalhes do cliente são apresentados nas seções 2, 3 e 4 do painel Líder.
2. Os detalhes do perfil do cliente são exibidos nesse espaço quando os editores estão conectados com uma função de proprietário. Se os editores estiverem conectados com uma função de colaborador, os detalhes nesta seção não estarão disponíveis.
3. O gráfico de rosca de Pedidos por SKUs exibe o detalhamento dos pedidos adquiridos para SKUs. São exibidos os 5 principais SKUs com a contagem de pedidos mais alta, enquanto o restante dos pedidos é agrupado em "Todo o restante".
4. O gráfico de rosca de Estações por SKUs exibe o detalhamento das estações solicitadas para SKUs. São exibidos os 5 principais SKUs com as estações mais altas, enquanto o restante dos pedidos é agrupado em Todo o restante.

### <a name="orders-and-usage-by-customer-type"></a>Pedidos e uso por tipo de cliente

O gráfico **Pedidos/uso por tipo de cliente** exibe o número de pedidos, uso normalizado e horas de uso bruto divididos entre clientes novos e existentes, com base na seleção de pedidos, uso normalizado e uso bruto no gráfico, respectivamente.

- Um cliente novo adquiriu uma ou mais ofertas ou relatou o uso pela primeira vez no mesmo mês do calendário (eixo y).
- Um cliente existente já adquiriu uma oferta de você ou relatou o uso anterior ao mês do calendário relatado (no eixo y).

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **Detalhes do cliente** exibe uma lista numerada dos 1.000 principais clientes classificados pela data em que adquiriram uma das ofertas pela primeira vez.

- As informações pessoais do cliente são disponibilizadas se o cliente consentir. Você só poderá exibir essas informações se tiver feito logon com um nível de função de proprietário de permissões. Saiba mais sobre as funções de usuário e as permissões.
- Cada coluna na grade pode ser classificada.
- Os dados podem ser extraídos para um arquivo TSV, se a contagem dos registros for menor que 1.000.
- Se o número de registros for superior a 1.000, os dados exportados são colocados de forma assíncrona em uma página de downloads pelos próximos 30 dias.
- Os filtros podem ser aplicados à tabela para exibir apenas os dados nos quais você está interessado. Os dados podem ser filtrados por nome da empresa, ID do cliente, ID da assinatura do marketplace, tipo de licença do Azure, data de aquisição, data de perda, email do cliente, país/região/estado/cidade/CEP do cliente, Idioma do cliente e assim por diante.
- Quando uma oferta é adquirida por um cliente protegido, as informações em **Dados detalhados do cliente** são mascaradas (* * * * * * * * * * * *).
- Os detalhes de dimensão do cliente, como nome da empresa, nome do cliente e email do cliente, estão no nível de ID da organização, e não no nível de transação do Azure Marketplace ou do AppSource.

### <a name="customer-page-filters"></a>Filtros de página do cliente

Os filtros da **página Clientes** são aplicados no nível da página Clientes. É possível selecionar vários filtros para renderizar o gráfico para os critérios que você escolher e os dados que deseja exibir na grade/exportação "Dados detalhados dos pedidos". Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página de pedidos.

>[!NOTE]
> As definições detalhadas de cada um dos campos na grade Cliente, os filtros de página e as possíveis seleções estão localizados em [ Perguntas frequentes e terminologia para análise do marketplace comercial](./faq-terminology.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no marketplace comercial do Partner Center, confira [Análise para o marketplace comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do marketplace para a oferta, confira [Painel de resumo na análise do marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre os pedidos em um formato gráfico para download, confira [Painel de pedidos na análise do marketplace comercial](./orders-dashboard.md).
- Para ofertas, uso e métricas de cobrança medidas da Máquina Virtual (VM), confira [Painel de uso na análise do marketplace comercial](./usage-dashboard.md).
- Para obter uma lista das solicitações de download nos últimos 30 dias, confira [Painel de downloads na análise do marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes sobre as ofertas no Azure Marketplace e no AppSource, confira [Painel de classificações e revisões na análise do marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do marketplace comercial e para um dicionário abrangente de termos de dados, confira [Perguntas frequentes e terminologia para análise do marketplace comercial](./faq-terminology.md).
