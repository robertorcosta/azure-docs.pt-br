---
title: Monitorar RU/s normalizado para um contêiner Cosmos do Azure ou uma conta
description: Saiba como monitorar o uso da unidade de solicitação normalizada de uma operação no Azure Cosmos DB. Os proprietários de uma conta de Azure Cosmos DB podem entender quais operações estão consumindo mais unidades de solicitação.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118800"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorar RU/s normalizados para um contêiner Cosmos do Azure ou uma conta

Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas de Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite ou configure nada explicitamente.

A métrica de **consumo de ru normalizada** é usada para ver quão bem saturadas são as réplicas WRT para o consumo de unidades de solicitação entre os intervalos de chaves de partição. Azure Cosmos DB distribui a taxa de transferência igualmente entre todas as partições físicas. Essa métrica fornece uma exibição por segundo da utilização máxima da taxa de transferência em um conjunto de réplicas. Usando essa métrica, se você vir uma alta porcentagem de utilização de unidades de solicitação, aumente a taxa de transferência para atender às necessidades de sua carga de trabalho.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando a RU/s normalizada é maior

Quando o consumo de RU/s normalizado atinge 100%, o cliente recebe erros de limitação de taxa. O cliente deve respeitar o tempo de espera e tentar novamente. Se houver um pequeno pico que atinja a utilização de 100%, significa que a taxa de transferência na réplica atingiu seu limite de desempenho máximo. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s em uma réplica, levará a um pequeno pico no consumo normalizado de RU/s. Nesses casos, não haverá nenhum erro de limitação de taxa imediata se a taxa de solicitação for baixa. Isso porque, Azure Cosmos DB permite que as solicitações sejam cobradas mais do que os RU/s provisionados para a solicitação específica e outras solicitações dentro desse período de tempo são limitadas por taxa.

As métricas de Azure Monitor ajudam a localizar as operações por código de status usando a métrica **total de solicitações** . Posteriormente, você pode filtrar essas solicitações pelo código de status 429 e dividi-las por **tipo de operação**.

Para localizar as solicitações que são limitadas por taxa, a maneira recomendada é obter essas informações por meio dos logs de diagnóstico.

Se houver um pico contínuo de 100% de consumo de RU/s normalizado ou perto de 100%, é recomendável aumentar a taxa de transferência. Você pode descobrir quais operações são pesadas e seu uso de pico utilizando as métricas do Azure monitor e os logs do Azure monitor.

A métrica de **consumo de ru normalizada** também é usada para ver qual intervalo de chave de partição está mais quente em termos de uso; dando a você a distorção da taxa de transferência em direção a um intervalo de chaves de partição. Posteriormente, você pode acompanhar para ver o log do **PartitionKeyRUConsumption** em logs de Azure monitor para obter informações sobre quais chaves de partição lógica estão quentes em termos de uso.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Exibir a métrica de consumo de unidade de solicitação normalizada

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**.

   ![Escolha uma conta do Azure Cosmos para exibir as métricas](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [métricas por categoria](monitor-cosmos-db-reference.md) . Neste exemplo, vamos selecionar métrica de **consumo de ru normalizada** e **Max** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro.

   ![Escolha uma métrica no portal do Azure](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo de unidade de solicitação normalizado

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**e **região**específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **CollectionName** e o valor correspondente no qual você está interessado. Em seguida, o grafo exibe as unidades de consumo de RU normalizadas consumidas para o contêiner para o período selecionado.  

Você pode agrupar as métricas usando a opção **aplicar divisão** .  

A métrica de consumo de unidade de solicitação normalizada para cada contêiner é exibida conforme mostrado na imagem a seguir:

![Aplicar filtros à métrica de consumo de unidade de solicitação normalizada](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>Próximas etapas

* Monitorar dados de Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Operações de plano de controle de Azure Cosmos DB de auditoria](audit-control-plane-logs.md)