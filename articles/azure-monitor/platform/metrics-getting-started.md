---
title: Começando com o explorador de métricas do Azure
description: Saiba como criar seu primeiro gráfico de métricas com o Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248769"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introdução ao Azure Metrics Explorer

## <a name="where-do-i-start"></a>Por onde começo
O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. Comece na seguinte ordem:

1. [Escolha um recurso e uma métrica](#create-your-first-metric-chart) e você verá um gráfico básico. Em [seguida, selecione um intervalo de tempo](#select-a-time-range) relevante para sua investigação.

1. Tente [aplicar filtros de dimensão e divisão](#apply-dimension-filters-and-splitting). Os filtros e a divisão permitem analisar quais segmentos da métrica contribuem para o valor métrico global e identificam possíveis outliers.

1. Use [configurações avançadas](#advanced-chart-settings) para personalizar o gráfico antes de fixar em painéis. [Configure alertas](alerts-metric-overview.md) para receber notificações quando o valor métrico exceder ou cair abaixo de um limite.

## <a name="create-your-first-metric-chart"></a>Crie seu primeiro gráfico métrico

Para criar um gráfico métrico, a partir do seu recurso, grupo de recursos, assinatura ou exibição do Azure Monitor, abra a guia Métricas e siga **estas** etapas:

1. Usando o seletor de recursos, selecione o recurso para o qual deseja ver métricas. (O recurso é pré-selecionado se você abriu **Métricas** no contexto de um recurso específico).

    > ![Selecionar um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, você deve escolher um namespace. O namespace é apenas uma maneira de organizar as métricas para que você possa encontrá-las com facilidade. Por exemplo, as contas de armazenamento têm namespaces separados para armazenar métricas de arquivos, tabelas, blobs e filas. Muitos tipos de recursos têm apenas um namespace.

3. Selecione uma métrica de uma lista de métricas disponíveis.

    > ![Selecione uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, você pode alterar a agregação métrica. Por exemplo, você pode querer que seu gráfico mostre valores mínimos, máximos ou médios da métrica.

> [!NOTE]
> Use o botão **Adicionar métrica** e repita essas etapas se quiser ver várias métricas plotadas no mesmo gráfico. Para vários gráficos em uma exibição, selecione o botão **Adicionar gráfico** no topo.

## <a name="select-a-time-range"></a>Selecione um intervalo de tempo

Por padrão, o gráfico mostra as últimas 24 horas de dados de métricas. Use o painel **de seleção de tempo** para alterar o intervalo de tempo, ampliar ou diminuir o zoom no gráfico. 

![Alterar o painel de intervalo de tempo](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Use o **pincel de tempo** para investigar uma área interessante do gráfico (pico ou um mergulho). Coloque o ponteiro do mouse no início da área, clique e segure o botão esquerdo do mouse, arraste para o outro lado da área e, em seguida, solte o botão. O gráfico será ampliado naquele intervalo de tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtragem](metrics-charts.md#apply-filters-to-charts) e [divisão](metrics-charts.md#apply-splitting-to-a-chart) são poderosas ferramentas de diagnóstico para as métricas que têm dimensões. Esses recursos mostram como vários segmentos métricos ("valores de dimensão") impactam o valor global da métrica e permitem identificar possíveis outliers.

- A **filtragem** permite que você escolha quais valores de dimensão serão incluídos no gráfico. Por exemplo, você pode querer mostrar solicitações bem-sucedidas ao mapear a métrica de tempo de resposta do *servidor.* Você precisaria aplicar o filtro sobre o sucesso da dimensão *de solicitação.* 

- A **divisão** controla se o gráfico exibe linhas separadas para cada valor de uma dimensão ou se agrega os valores em uma única linha. Por exemplo, você pode ver uma linha para um tempo médio de resposta em todas as instâncias do servidor ou ver linhas separadas para cada servidor. Você precisaria aplicar a divisão na dimensão de instância do *servidor* para ver linhas separadas.

Confira [exemplos de gráficos](metric-chart-samples.md) que têm a filtragem e a divisão aplicadas. O artigo mostra que as etapas foram usadas para configurar os gráficos.

## <a name="advanced-chart-settings"></a>Configurações de gráfico avançadas

Você pode personalizar o estilo do gráfico, o título e modificar as configurações avançadas do gráfico. Quando terminar a personalização, fixe-a em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Siga a [documentação do produto](metrics-charts.md) para saber sobre esses e outros recursos avançados do explorador de métricas do Azure Monitor.

## <a name="next-steps"></a>Próximas etapas

* [Conheça os recursos avançados do Metrics Explorer](metrics-charts.md)
* [Solução de problemas do Metrics Explorer](metrics-troubleshoot.md)
* [Veja uma lista das métricas disponíveis para os serviços do Azure](metrics-supported.md)
* [Veja exemplos de gráficos configurados](metric-chart-samples.md)
