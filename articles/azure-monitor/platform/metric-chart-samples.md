---
title: Exemplo de gráfico de métricas do Azure Monitor
description: Saiba mais sobre como visualizar seus dados do Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9b2ab664f319de07fd70bd1a22b1ba6d64ac208f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320248"
---
# <a name="metric-chart-examples"></a>Exemplos de gráfico de métrica 

A plataforma do Azure oferece [mais de mil métricas](./metrics-supported.md), muitas das quais têm dimensões. Ao usar [filtros de dimensão](./metrics-charts.md), aplicar a [divisão](./metrics-charts.md), controlar o tipo de gráfico e ajustar as configurações do gráfico você pode criar painéis e exibições de diagnóstico poderosos que fornecem insight sobre a integridade da sua infraestrutura e seus aplicativos. Este artigo mostra alguns exemplos dos gráficos que podem ser criados usando o [Metrics Explorer](./metrics-charts.md) e explica as etapas necessárias para configurar cada um deles.

Deseja compartilhar seus ótimos exemplos de gráficos com o mundo? Contribua com esta página no GitHub e compartilhe seus próprios exemplos de gráfico aqui mesmo!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização de CPU do site por instâncias de servidor

Este gráfico mostra se a CPU para um Serviço de Aplicativo estava dentro do intervalo aceitável e a divide por instância para determinar se a carga foi distribuída adequadamente. Você pode ver no gráfico que o aplicativo estava em execução em uma única instância de servidor antes das 6h e, em seguida, foi escalado verticalmente adicionando outra instância.

![Gráfico de linhas do percentual médio de CPU por instância do servidor](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o recurso do Serviço de Aplicativo e encontre a métrica **Percentual de CPU**. Em seguida, clique em **Aplicar a divisão** e selecione a dimensão **Instância**.

## <a name="application-availability-by-region"></a>Disponibilidade do aplicativo por região

Exiba a disponibilidade do aplicativo por região para identificar quais localizações geográficas estão tendo problemas. Este gráfico mostra a métrica de disponibilidade do Application Insights. Você pode ver que o aplicativo monitorado não tem problemas com a disponibilidade do datacenter do Leste dos EUA, mas ele está enfrentando um problema de disponibilidade parcial do Oeste dos EUA e Leste da Ásia.

![Gráfico de disponibilidade média por localizações](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Você precisa primeiro ativar o monitoramento da [Disponibilidade do Application Insights](../app/monitor-web-app-availability.md) para seu site. Depois disso, escolha o recurso do Application Insights e selecione a métrica Disponibilidade. Aplique a divisão na dimensão **Localização da execução**.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de transações de conta de armazenamento pelo nome da API

O recurso da conta de armazenamento está enfrentando um volume de transações em excesso. Você pode usar a métrica de transações para identificar qual API é responsável pela carga em excesso. Observe que o gráfico a seguir está configurado com a mesma dimensão (nome da API) na filtragem e na divisão para restringir a exibição apenas às chamadas à API de interesse:

![Gráfico de barras de transações de API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No seletor de métrica, selecione sua conta de armazenamento e a métrica **Transações**. Alterne o tipo de gráfico para **Gráfico de barras**. Clique em **Aplicar a divisão** e selecione a dimensão **Nome da API**. Em seguida, clique em **Adicionar filtro** e escolha a dimensão **Nome da API** novamente. Na caixa de diálogo de filtro, selecione as APIs que deseja plotar no gráfico.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [Pastas de Trabalho](./workbooks-overview.md) do Azure Monitor
* Saiba mais sobre o [Metrics Explorer](metrics-charts.md)

