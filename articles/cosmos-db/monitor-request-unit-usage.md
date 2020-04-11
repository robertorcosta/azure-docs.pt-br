---
title: Monitore o uso de throughput de uma operação no Azure Cosmos DB
description: Saiba como monitorar o throughput ou solicitar o uso da unidade de uma operação no Azure Cosmos DB. Os proprietários de uma conta do Azure Cosmos DB podem entender quais operações estão tomando mais unidades de Solicitação.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115424"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Como monitorar o throughput ou solicitar o uso da unidade de uma operação no Azure Cosmos DB

O Azure Monitor for Azure Cosmos DB fornece uma visualização métrica para monitorar sua conta e criar dashboards. As métricas do Azure Cosmos DB são coletadas por padrão, esse recurso não exige que você habilite ou configure nada explicitamente. A métrica **Total Request Units** é usada para fazer com que as unidades de solicitação usem para diferentes tipos de operações. Mais tarde você pode analisar quais operações usaram a maior parte do throughput. Por padrão, os dados de throughput são agregados em intervalo de um minuto. No entanto, você pode alterar a unidade de agregação alterando a opção de granularidade temporal.

Existem duas maneiras de analisar os dados de uso da unidade de solicitação:

* Dentro do determinado intervalo de tempo que as operações estão tomando mais unidades de solicitação.
* Quais operações em geral dominam sua carga de trabalho consumindo mais unidades de solicitação.
Esta análise permite que você se concentre em operações como inserir, upsert e olhar para sua indexação. Você pode descobrir se você está acima/sob a indexação de campos específicos e modificar a [política de indexação](index-policy.md#include-exclude-paths) para incluir ou excluir os caminhos.

Se você notar que certas consultas estão tomando mais unidades de solicitação, você pode tomar ações como:

* Reconsidere se você está solicitando a quantidade certa de dados.
* Modifique a consulta para usar o índice com cláusula de filtro.
* Execute chamadas de função UDF mais baratas.
* Defina as teclas de partição para minimizar o ventilador de consulta em diferentes partições.
* Você também pode usar as métricas de consulta retornadas na resposta de chamada, os detalhes do registro de diagnóstico e consultar o artigo de [ajuste de desempenho](sql-api-query-metrics.md) da consulta para saber mais sobre a execução da consulta.
* Você pode começar a partir da soma e, em seguida, olhar para a utilização avg usando a dimensão certa.

## <a name="view-the-total-request-unit-usage-metric"></a>Exibir a métrica de uso total da unidade de solicitação

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. No painel **Métricas** > **Selecione um recurso** > escolha a **assinatura**necessária e o grupo **de recursos**. Para o **tipo Recurso,** selecione **contas Azure Cosmos DB,** escolha uma de suas contas azure Cosmos existentes e **selecione Aplicar**.

   ![Escolha a conta Azure Cosmos DB para visualizar métricas](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica **Total Request Units** na lista de métricas disponíveis. Para saber em detalhes todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria.](monitor-cosmos-db-reference.md) Neste exemplo, vamos selecionar **Unidades de Solicitação Total** e **Avg** como o valor de agregação. Além desses detalhes, você também pode selecionar o **intervalo de tempo** e a **granularidade** de tempo das métricas. No máximo, você pode visualizar métricas para os últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal Azure](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtros para uso da unidade de solicitação

Você também pode filtrar métricas e obter os gráficos exibidos por um Nome de **Coleção**específico, **Nome do banco de dados,** **Tipo de Operação,** **Região,** **Status**e **StatusCode**. As opções **de divisão Adicionar** e **aplicar** permitem filtrar o uso da unidade de solicitação e agrupar as métricas.

Para obter o uso da unidade de solicitação de cada operação, seja por total(soma) ou média, **selecione Aplicar divisão** e escolher o **tipo de operação** e o valor do filtro conforme mostrado na imagem a seguir:

   ![Unidades de solicitação do Cosmos DB para operações no monitor Azure](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Se você quiser ver o uso da unidade de solicitação por coleta, **selecione Aplicar divisão** e escolha o nome da coleção como um filtro. Você verá um bate-papo como o seguinte com uma escolha de coleções dentro do painel. Em seguida, você pode selecionar um nome de coleção específico para exibir mais detalhes:

   ![Cosmos DB Solicitar unidades para todas as operações da coleção no monitor Azure](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Próximas etapas

* Monitore os dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditoria Azure Cosmos DB controla operações de aviões](audit-control-plane-logs.md)