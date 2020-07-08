---
title: Monitorar RU/s normalizado para um contêiner Cosmos do Azure ou uma conta
description: Saiba como monitorar o uso da unidade de solicitação normalizada de uma operação no Azure Cosmos DB. Os proprietários de uma conta de Azure Cosmos DB podem entender quais operações estão consumindo mais unidades de solicitação.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482897"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorar RU/s normalizados para um contêiner Cosmos do Azure ou uma conta

O Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas do Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite nem configure nada explicitamente.

A métrica de **consumo de ru normalizada** é usada para ver quão bem saturadas as réplicas estão relacionadas ao consumo de unidades de solicitação entre os intervalos de chaves de partição. Azure Cosmos DB distribui a taxa de transferência igualmente entre todas as partições físicas. Essa métrica fornece uma exibição por segundo da utilização máxima da taxa de transferência em um conjunto de réplicas. Use essa métrica para calcular o uso de RU/s em partições para um determinado contêiner. Usando essa métrica, se você vir uma alta porcentagem de utilização de unidades de solicitação, aumente a taxa de transferência para atender às necessidades de sua carga de trabalho.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando a RU/s normalizada é maior

Quando o consumo de RU/s normalizado atinge 100%, o cliente recebe erros de limitação de taxa. O cliente deve respeitar o tempo de espera e tentar novamente. Se houver um pequeno pico que atinja a utilização de 100%, significa que a taxa de transferência na réplica atingiu seu limite de desempenho máximo. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s em uma réplica, levará a um pequeno pico no consumo normalizado de RU/s. Nesses casos, não haverá nenhum erro de limitação de taxa imediata se a taxa de solicitação for baixa. Isso porque, Azure Cosmos DB permite que as solicitações sejam cobradas mais do que os RU/s provisionados para a solicitação específica e outras solicitações dentro desse período de tempo são limitadas por taxa.

As métricas de Azure Monitor ajudam a localizar as operações por código de status usando a métrica **total de solicitações** . Posteriormente, você pode filtrar essas solicitações pelo código de status 429 e dividi-las por **tipo de operação**.

Para localizar as solicitações que são limitadas por taxa, a maneira recomendada é obter essas informações por meio dos logs de diagnóstico.

Se houver um pico contínuo de 100% de consumo de RU/s normalizado ou perto de 100%, é recomendável aumentar a taxa de transferência. Você pode descobrir quais operações são pesadas e seu uso de pico utilizando as métricas do Azure monitor e os logs do Azure monitor.

A métrica de **consumo de ru normalizada** também é usada para ver qual intervalo de chave de partição está mais quente em termos de uso; dando a você a distorção da taxa de transferência em direção a um intervalo de chaves de partição. Posteriormente, você pode acompanhar para ver o log do **PartitionKeyRUConsumption** em logs de Azure monitor para obter informações sobre quais chaves de partição lógica estão quentes em termos de uso.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Exibir a métrica de consumo de unidade de solicitação normalizada

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

3. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Escolha uma conta do Azure Cosmos para exibir as métricas":::

4. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar métrica de **consumo de ru normalizada** e **Max** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Escolha uma métrica no portal do Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo de unidade de solicitação normalizado

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**e **região**específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **CollectionName** e o valor correspondente no qual você está interessado. Em seguida, o grafo exibe as unidades de consumo de RU normalizadas consumidas para o contêiner para o período selecionado.  

Você pode agrupar as métricas usando a opção **Aplicar divisão**.  

A métrica de consumo de unidade de solicitação normalizada para cada contêiner é exibida conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Aplicar filtros à métrica de consumo de unidade de solicitação normalizada":::

## <a name="next-steps"></a>Próximas etapas

* Monitore dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditar operações do painel de controle do Azure Cosmos DB](audit-control-plane-logs.md)
