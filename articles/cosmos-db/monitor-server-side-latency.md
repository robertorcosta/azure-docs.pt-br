---
title: Como monitorar a latência do lado do servidor para operações no Azure Cosmos DB
description: Saiba como monitorar a latência do servidor para operações na conta Azure Cosmos DB ou em um contêiner. Os proprietários de uma conta azure Cosmos DB podem entender os problemas de latência do lado do servidor com suas contas do Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113916"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorar a latência do lado do servidor para operações em um contêiner ou conta DO Azure Cosmos

O Azure Monitor for Azure Cosmos DB fornece uma visualização métrica para monitorar sua conta e criar dashboards. As métricas do Azure Cosmos DB são coletadas por padrão, esse recurso não exige que você habilite ou configure nada explicitamente. A métrica de latência do lado do servidor é usada para visualizar a latência do lado do servidor de uma operação. O Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/gravação de ponto com conectividade direta. Para operações de leitura e gravação de pontos, os SLAs são calculados conforme detalhado no [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Se você vir uma latência extraordinariamente grande para operações pontuais, tais como:

* Uma operação get ou set com chave de partição e ID no modo direto
* Uma operação de leitura ou gravação ou
* Uma consulta

Você pode procurar o registro de diagnóstico para ver o tamanho dos dados retornados. Se você vir uma latência sustentada de alta latência para operações de consulta, você poderá procurar o registro de diagnóstico para obter o tamanho dos dados retornados, [throughput ou RU/s usados](cosmosdb-monitor-resource-logs.md#diagnostic-queries) ou o número de tais operações em um determinado período. Dessa forma, você pode depurar os problemas de latência do lado do servidor.

## <a name="view-the-server-side-latency-metric"></a>Exibir a métrica de latência do lado do servidor

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. No painel **Métricas** > **Selecione um recurso** > escolha a **assinatura**necessária e o grupo **de recursos**. Para o **tipo Recurso,** selecione **contas Azure Cosmos DB,** escolha uma de suas contas azure Cosmos existentes e **selecione Aplicar**.
   
   ![Escolha a conta Azure Cosmos DB para visualizar métricas](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica **de Latência** do lado do servidor na lista de métricas disponíveis. Para saber em detalhes todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria.](monitor-cosmos-db-reference.md) Neste exemplo, vamos selecionar **Latência lateral** do servidor e **Avg** como o valor de agregação. Além desses detalhes, você também pode selecionar o **intervalo de tempo** e a **granularidade** de tempo das métricas. No máximo, você pode visualizar métricas para os últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver a latência do lado do servidor por minuto durante o período selecionado.  

   ![Escolha a métrica de latência do lado do servidor no portal Azure](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Você também pode filtrar métricas e obter os gráficos exibidos por um **Nome**de Coleta específico, **Modo de Conexão,** **Nome de Banco de Dados,** **OperaçãoTipo,** **Região**e **PublicAPIType**. 

Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType** e selecione o valor **sql**. Adicione outro filtro para **OperationType**. Em seguida, o gráfico exibe a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas via procedimento armazenado não são registradas, portanto não estão disponíveis sob a métrica OperationType.

As métricas **de latência do lado do servidor** para cada operação são exibidas conforme mostrado na imagem a seguir:

![Filtros para métricas de latência do lado do servidor](./media/monitor-server-side-latency/server-side-latency-filters.png)

Você também pode agrupar as métricas usando a opção **Aplicar divisão.**  

## <a name="next-steps"></a>Próximas etapas

* Monitore os dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditoria Azure Cosmos DB controla operações de aviões](audit-control-plane-logs.md)