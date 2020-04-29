---
title: Monitorar o uso da taxa de transferência de uma operação no Azure Cosmos DB
description: Saiba como monitorar a taxa de transferência ou o uso da unidade de solicitação de uma operação no Azure Cosmos DB. Os proprietários de uma conta de Azure Cosmos DB podem entender quais operações estão levando mais unidades de solicitação.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115424"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Como monitorar a taxa de transferência ou o uso da unidade de solicitação de uma operação no Azure Cosmos DB

Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas de Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite ou configure nada explicitamente. A métrica de **unidades de solicitação total** é usada para obter o uso de unidades de solicitação para diferentes tipos de operações. Posteriormente, você pode analisar quais operações usaram a maior parte da taxa de transferência. Por padrão, os dados de taxa de transferência são agregados em um intervalo de um minuto. No entanto, você pode alterar a unidade de agregação alterando a opção tempo de granularidade.

Há duas maneiras de analisar os dados de uso da unidade de solicitação:

* Dentro do intervalo de tempo determinado, quais operações estão levando mais unidades de solicitação.
* Quais operações em geral dominam sua carga de trabalho consumindo mais unidades de solicitação.
Essa análise permite que você se concentre em operações como inserir, Upsert e examinar sua indexação. Você pode descobrir se está de acordo com a indexação de campos específicos e modificar a [política de indexação](index-policy.md#include-exclude-paths) para incluir ou excluir os caminhos.

Se você observar que determinadas consultas estão levando mais unidades de solicitação, poderá executar ações como:

* Reconsidere se você estiver solicitando a quantidade certa de dados.
* Modifique a consulta para usar o índice com a cláusula de filtro.
* Execute chamadas de função UDF menos dispendiosas.
* Defina chaves de partição para minimizar o ventilador fora da consulta em partições diferentes.
* Você também pode usar as métricas de consulta retornadas na resposta da chamada, os detalhes do log de diagnóstico e consultar o artigo de [ajuste de desempenho de consulta](sql-api-query-metrics.md) para saber mais sobre a execução da consulta.
* Você pode começar da soma e, em seguida, examinar a utilização média usando a dimensão direita.

## <a name="view-the-total-request-unit-usage-metric"></a>Exibir a métrica de uso da unidade de solicitação total

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**.

   ![Escolha a conta de Azure Cosmos DB para exibir as métricas](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica de **unidades de solicitação total** na lista de métricas disponíveis. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [métricas por categoria](monitor-cosmos-db-reference.md) . Neste exemplo, vamos selecionar total de **unidades de solicitação** e **Méd** como o valor de agregação. Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal do Azure](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtros para uso da unidade de solicitação

Você também pode filtrar as métricas e obter os gráficos exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **região**, **status**e **StatusCode**específicos. As opções **Adicionar filtro** e **aplicar divisão** permitem filtrar o uso da unidade de solicitação e agrupar as métricas.

Para obter o uso da unidade de solicitação de cada operação por total (Sum) ou Average, selecione **aplicar divisão** e escolha o **tipo de operação** e o valor do filtro, conforme mostrado na imagem a seguir:

   ![Cosmos DB unidades de solicitação para operações no Azure monitor](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Se você quiser ver o uso da unidade de solicitação por coleção, selecione **aplicar divisão** e escolha o nome da coleção como um filtro. Você verá um chat semelhante ao seguinte, com uma opção de coleções no painel. Em seguida, você pode selecionar um nome de coleção específico para exibir mais detalhes:

   ![Cosmos DB unidades de solicitação para todas as operações pela coleção no Azure monitor](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Próximas etapas

* Monitorar dados de Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Operações de plano de controle de Azure Cosmos DB de auditoria](audit-control-plane-logs.md)