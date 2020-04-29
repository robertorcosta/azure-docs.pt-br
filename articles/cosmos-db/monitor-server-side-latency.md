---
title: Como monitorar a latência do lado do servidor para operações no Azure Cosmos DB
description: Saiba como monitorar a latência do servidor para operações em Azure Cosmos DB conta ou um contêiner. Os proprietários de uma conta de Azure Cosmos DB podem entender os problemas de latência do lado do servidor com suas contas do Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113916"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorar a latência do lado do servidor para operações em um Azure Cosmos DB contêiner ou uma conta

Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas de Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite ou configure nada explicitamente. A métrica de latência do lado do servidor é usada para exibir a latência do lado do servidor de uma operação. Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/gravação de ponto com conectividade direta. Para operações de leitura e gravação de ponto, os SLAs são calculados como detalhados no [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Se você vir uma latência excepcionalmente grande para operações de ponto como:

* Uma operação get ou set com chave de partição e ID no modo direto
* Uma operação de leitura ou gravação ou
* Uma consulta

Você pode pesquisar o log de diagnóstico para ver o tamanho dos dados retornados. Se você vir uma alta latência sustentada para operações de consulta, poderá pesquisar o log de diagnóstico quanto ao tamanho dos dados retornados, da [taxa de transferência ou de ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) usados, ou o número de operações desse tipo em um determinado período. Dessa forma, você pode depurar os problemas de latência do lado do servidor.

## <a name="view-the-server-side-latency-metric"></a>Exibir a métrica de latência do lado do servidor

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**.
   
   ![Escolha a conta de Azure Cosmos DB para exibir as métricas](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica de **latência do lado do servidor** na lista de métricas disponíveis. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [métricas por categoria](monitor-cosmos-db-reference.md) . Neste exemplo, vamos selecionar latência do **lado do servidor** e **Méd** como o valor de agregação. Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver a latência do lado do servidor por minuto para o período selecionado.  

   ![Escolha a métrica de latência do lado do servidor no portal do Azure](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Você também pode filtrar as métricas e obter os gráficos exibidos por um **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **região**e **PublicAPIType**específicos. 

Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType** , e selecione o valor **SQL**. Adicione outro filtro para **OperationType**. Em seguida, o grafo exibe a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas para que não estejam disponíveis na métrica OperationType.

As métricas de **latência do lado do servidor** para cada operação são exibidas conforme mostrado na imagem a seguir:

![Filtros para métricas de latência do lado do servidor](./media/monitor-server-side-latency/server-side-latency-filters.png)

Você também pode agrupar as métricas usando a opção **aplicar divisão** .  

## <a name="next-steps"></a>Próximas etapas

* Monitorar dados de Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Operações de plano de controle de Azure Cosmos DB de auditoria](audit-control-plane-logs.md)