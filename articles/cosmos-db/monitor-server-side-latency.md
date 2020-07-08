---
title: Como monitorar a latência do lado do servidor para operações no Azure Cosmos DB
description: Saiba como monitorar a latência do servidor para operações em Azure Cosmos DB conta ou um contêiner. Os proprietários de uma conta de Azure Cosmos DB podem entender os problemas de latência do lado do servidor com suas contas do Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262694"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorar a latência do lado do servidor para operações em um Azure Cosmos DB contêiner ou uma conta

O Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas do Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite nem configure nada explicitamente. A métrica de latência do lado do servidor é usada para exibir a latência do lado do servidor de uma operação. Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/gravação de ponto com conectividade direta. Para operações de leitura e gravação de ponto, os SLAs são calculados como detalhados no [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Se você vir uma latência excepcionalmente grande para operações de ponto como:

* Uma operação get ou set com chave de partição e ID no modo direto
* Uma operação de leitura ou gravação ou
* Uma consulta

Você pode pesquisar o log de diagnóstico para ver o tamanho dos dados retornados. Se você vir uma alta latência sustentada para operações de consulta, poderá pesquisar o log de diagnóstico quanto ao tamanho dos dados retornados, da [taxa de transferência ou de ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) usados, ou o número de operações desse tipo em um determinado período. Dessa forma, você pode depurar os problemas de latência do lado do servidor.

## <a name="view-the-server-side-latency-metric"></a>Exibir a métrica de latência do lado do servidor

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

1. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Escolha a conta do Azure Cosmos DB para exibir as métricas":::

1. Em seguida, selecione a métrica de **latência do lado do servidor** na lista de métricas disponíveis. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar latência do **lado do servidor** e **Méd** como o valor de agregação. Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro. Você pode ver a latência do lado do servidor por minuto para o período selecionado.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Escolha a métrica de latência do lado do servidor no portal do Azure":::

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Você também pode filtrar as métricas e obter os gráficos exibidos por um **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **região**e **PublicAPIType**específicos. 

Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType** , e selecione o valor **SQL**. Adicione outro filtro para **OperationType**. Em seguida, o grafo exibe a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas em log, de modo que não estão disponíveis na métrica OperationType.

As métricas de **latência do lado do servidor** para cada operação são exibidas conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtros para métricas de latência do lado do servidor":::

Você também pode agrupar as métricas usando a opção **aplicar divisão** .  

## <a name="next-steps"></a>Próximas etapas

* Monitore dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditar operações do painel de controle do Azure Cosmos DB](audit-control-plane-logs.md)
