---
title: Painel do cliente em análises de mercado comercial no Partner Center
description: Saiba como acessar informações sobre seus clientes, incluindo tendências de crescimento, usando o painel do cliente em análises de Mercado Comercial.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 7fe56a81bc82f9d49cf9f81b30986882b6d13cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286108"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Painel do Cliente em análises de mercado comercial

Este artigo fornece informações sobre o painel do **cliente** no Partner Center. Este painel exibe informações sobre seus clientes, incluindo tendências de crescimento, apresentadas em um formato gráfico e para download.

Para acessar o painel do **Cliente,** abra o painel **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Mercado Comercial.

>[!NOTE]
> Para definições detalhadas da terminologia de análise, consulte [perguntas freqüentes e terminologia para análises de mercado comercial](./faq-terminology.md).

## <a name="customer-dashboard"></a>Painel de cliente

O **painel cliente** do menu **Analisar** exibe dados para clientes que adquiriram suas ofertas. Você pode visualizar representações gráficas dos seguintes itens:

- [Resumo do cliente](#customer-summary)
- [Cliente por geografia](#customer-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por pedidos e uso](#customers-by-orders-and-usage)
- [Clientes por SKUs](#customers-by-skus)
- [Pedidos e uso por tipo de cliente](#orders-and-usage-by-customer-type)
- [Tabela de detalhes do cliente](#customer-details-table)
- [Filtros de página do cliente](#customer-page-filters)

### <a name="customer-summary"></a>Resumo do cliente

A seção Resumo do Cliente exibe uma contagem de todos os clientes, incluindo novos, existentes e agitados, durante a faixa de data selecionada.

- O total de clientes é definido como a contagem de todos os clientes que compraram sua oferta e têm pelo menos um pedido que não foi cancelado.
- A porcentagem de crescimento dos clientes em relação ao mês anterior é indicada pelo número e indicador para cima no indicador verde ou para baixo no vermelho.
- As tendências de crescimento são representadas por gráficos de barras e exibirão o valor para cada mês, pairando sobre as colunas do gráfico.

Existem três **tipos de clientes**: novos, existentes e agitados.

- Um novo cliente adquiriu uma ou mais de suas ofertas pela primeira vez dentro do mês selecionado.
- Um cliente existente adquiriu uma ou mais de suas ofertas antes do mês selecionado.
- Um cliente agitado cancelou todas as ofertas compradas anteriormente.

### <a name="customer-by-geography"></a>Cliente por geografia

O **gráfico Cliente por geografia** mostra as contagens de todos os clientes e clientes adquiridos durante a faixa de data selecionada e são mapeados com base no País do Cliente. A cor clara para escura no mapa representa o baixo a alto valor da contagem de clientes. Clique em um registro na tabela para ampliar um país.

O mapa de calor exibe a contagem de clientes e % por país cliente. Você pode mover o mapa para ver a localização exata e ampliar para um local específico. Este mapa possui uma grade suplementar que permite visualizar o % dos clientes por localização, bem como clientes recém-adicionados a esse local.

### <a name="customer-trends"></a>Tendências do cliente

O **gráfico de tendências do cliente** exibe uma contagem de todos os clientes, incluindo novos, existentes e agitados, com uma tendência de crescimento mês a mês.

- O gráfico de linhas representa os percentuais globais de crescimento do cliente.
- A coluna do mês representa a contagem de clientes empilhados por clientes novos, existentes e agitados.
- A contagem de clientes churned é exibida na direção negativa do Eixo Y.
- Você pode selecionar itens de legenda específicos para exibir exibições mais detalhadas. Por exemplo, selecione novos clientes da legenda para exibir apenas novos clientes.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda no eixo x e focar em pontos de dados específicos para visualizar com mais detalhes.
- Pairando sobre uma coluna do gráfico exibirá detalhes apenas para esse mês.

### <a name="customers-by-orders-and-usage"></a>Clientes por pedidos e uso

O **gráfico Clientes por pedidos/uso** tem três guias, "pedidos", "uso normalizado" e "uso bruto". O **percentil superior do cliente** é exibido ao longo do eixo x, conforme determinado pelo número de pedidos. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (gráfico de linha) exibe a porcentagem cumulativa do número total de pedidos. Você pode exibir detalhes pairando sobre pontos ao longo do gráfico de linha.

Como exemplo, veja o gráfico abaixo para uso normalizado: O 30º percentil superior dos clientes está contribuindo para 87% do uso normalizado cumulativamente. O 30º percentil de clientes está contribuindo apenas com 1,57 milhão de horas de uso.

### <a name="customers-by-skus"></a>Clientes por SKUs

Os **clientes por SKUs/gráficos de uso** são descritos abaixo.

1. A placa Leader apresenta detalhes dos 50 maiores clientes classificados por contagem de pedidos. Após a seleção de um cliente, os detalhes do cliente são apresentados nas seções 2, 3 e 4 deste quadro de líderes.
2. Os detalhes do perfil do Cliente são exibidos neste espaço quando os editores são conectados com uma função de proprietário. Se os editores estiverem conectados com uma função de contribuinte, os detalhes desta seção não estarão disponíveis.
3. O gráfico de donuts Orders by SKUs exibe a divisão das ordens compradas para SKUs. As 5 SKUs mais altas com a maior contagem de pedidos são exibidas, enquanto o resto das ordens são agrupadas em 'rest all'.
4. O gráfico de donuts Seats by SKUs exibe a quebra dos assentos pedidos para SKUs. Os 5 Melhores SKUs com os assentos mais altos são exibidos, enquanto o resto das encomendas são agrupadas repouso.

### <a name="orders-and-usage-by-customer-type"></a>Pedidos e uso por tipo de cliente

O **gráfico Pedidos/uso por tipo de cliente** exibe o número de pedidos, uso normalizado e horas de uso bruto divididas entre novos clientes e clientes existentes; com base na seleção de pedidos, normalizados e uso bruto, respectivamente, no gráfico.

- Um novo cliente adquiriu uma ou mais de suas ofertas ou informou o uso pela primeira vez dentro do mesmo mês (eixo y).
- Um cliente existente já adquiriu uma oferta de você ou relatou o uso antes do mês calendário relatado (no eixo y).

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **de detalhes do cliente** exibe uma lista numerada dos 1000 maiores clientes classificados pela data em que adquiriram uma de suas ofertas.

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver fornecido o consentimento. Você só pode visualizar essas informações se tiver logado com um nível de permissões do proprietário. Saiba mais sobre funções e permissões do usuário.
- Cada coluna na grade é classificada.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros for superior a 1000, os dados exportados serão colocados assíncronamente em uma página de downloads pelos próximos 30 dias.
- Os filtros podem ser aplicados à tabela para exibir apenas os dados que você está interessado. Os dados podem ser filtrados pelo nome da empresa, ID do cliente, ID de assinatura de marketplace, tipo de licença do Azure, data adquirida, data perdida, e-mail do cliente, país do cliente/estado/cidade/zip, idioma do cliente, etc.

### <a name="customer-page-filters"></a>Filtros de página do cliente

Os filtros de **página Clientes** são aplicados no nível da página Clientes. Você pode selecionar vários filtros para renderizar o gráfico para os critérios que você escolher para visualizar e os dados que deseja ver na grade /exportação 'Dados de pedidos detalhados'. Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página de pedidos.

>[!NOTE]
> Definições detalhadas para cada um dos campos na grade do Cliente, filtros de página e suas possíveis seleções estão localizadas em [perguntas e terminologias freqüentes para análise de mercado comercial.](./faq-terminology.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [Analytics for the Commercial Marketplace in Partner Center](./analytics.md).
- Para obter gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para sua oferta, consulte [Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e para download, consulte [Orders Dashboard em análises de mercado comercial](./orders-dashboard.md).
- Para a Máquina Virtual (VM) oferece métricas de uso e faturamento medido, consulte [O Painel de Uso em análises de mercado comercial](./usage-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte [O Painel de Downloads em análises de Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [Ratings e reviews dashboard em análises de Mercado Comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre análises de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas e terminologias freqüentes para análises de mercado comercial](./faq-terminology.md).
