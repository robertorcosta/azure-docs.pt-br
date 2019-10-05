---
title: Obter Azure Cosmos DB métricas de Azure Monitor
description: Saiba como exibir diferentes categorias de Azure Cosmos DB métricas de Azure Monitor usando portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973733"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorar e depurar Azure Cosmos DB métricas de Azure Monitor

Você pode exibir Azure Cosmos DB métricas de Azure Monitor API. O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o portal do Azure, acessá-los por meio da API REST ou consultá-los usando o PowerShell ou a CLI. Azure Cosmos DB métricas são valores numéricos de baixa latência, coletados em frequência de um minuto por padrão, você também pode agregar essas métricas. Essas métricas são capazes de dar suporte a cenários em tempo real.  

Este artigo descreve as diferentes métricas de Azure Cosmos DB que você pode exibir de Azure Monitor usando portal do Azure. Se você estiver interessado em casos de uso comuns e como Azure Cosmos DB métricas reutilizadas para analisar e depurar esses problemas, consulte [monitorar e depurar com métricas no artigo Azure Cosmos DB](use-metrics.md) . Você usará uma das contas do Azure Cosmos existentes e exibirá as diferentes métricas nos níveis de banco de dados, contêiner, região, solicitação ou operação. Portanto, verifique se você tem uma conta do Azure cosmos com dados de exemplo e execute operações CRUD nesses dados.

## <a name="view-metrics-from-azure-portal"></a>Exibir métricas de portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**. 

   ![Escolha uma conta de Cosmos DB para exibir as métricas](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte a seção [métricas por categoria](#metrics-by-category) deste artigo. Neste exemplo, vamos selecionar unidades de **solicitação** e **Méd** como o valor de agregação. 

   Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal do Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Adicionar filtros a métricas

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **região**e **StatusCode**específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** , e selecione um valor como **consulta**. Em seguida, o grafo exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas para que não estejam disponíveis na métrica OperationType.

![Adicionar um filtro para selecionar a granularidade da métrica](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Você pode agrupar as métricas usando a opção **aplicar divisão** . Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o grafo para todas as operações de uma só vez, conforme mostrado na imagem a seguir: 

![Adicionar filtro de divisão de aplicação](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Métricas por categoria

### <a name="request-metrics"></a>Métricas de solicitação
            
|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| TotalRequests (total de solicitações) | Contagem (contagem) | Número de solicitações feitas| DatabaseName, CollectionName, Region, StatusCode| Todas | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro interno do servidor, Serviço Não Disponível, Solicitações Limitadas, Média de Solicitações por Segundo | Usado para monitorar solicitações por código de status, contêiner a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60. |
| MetadataRequests (solicitações de metadados) |Contagem (contagem) | Contagem de solicitações de metadados. Azure Cosmos DB mantém o contêiner de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Todas| |Usada para monitorar os limitadores devido a solicitações de metadados.|
| MongoRequests (solicitações de Mongo) | Contagem (contagem) | Número de Solicitações do Mongo Feitas | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todas |Taxa de Solicitação de Consulta do Mongo, Taxa de Solicitação de Atualização do Mongo, Taxa de Solicitação de Exclusão do Mongo, Taxa de Solicitação de Inserção do Mongo, Taxa de Solicitação de Contagem do Mongo| Usada para monitorar os erros de solicitação do Mongo, usos por tipo de comando. |

### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (encargo de solicitação Mongo) | Contagem (total) |Unidades Solicitadas do Mongo Consumidas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todas |Encargo de Solicitação de Consulta do Mongo, Encargo de Solicitação de Atualização do Mongo, Encargo de Solicitação de Exclusão do Mongo, Encargo de Solicitação de Inserção do Mongo, Encargo de Solicitação de Contagem do Mongo| Usada para monitorar RUs de recurso do Mongo em um minuto.|
| TotalRequestUnits (total de unidades de solicitação)| Contagem (total) | Unidades Solicitadas Consumidas| DatabaseName, CollectionName, Region, StatusCode |Todas| TotalRequestUnits| Usada para monitorar o uso de RU Total a uma granularidade de minuto. Para obter a média de RU consumida por segundo, use a agregação total no minuto e divida por 60.|
| ProvisionedThroughput (taxa de transferência provisionada)| Contagem (máximo) |Taxa de transferência provisionada na granularidade do contêiner| DatabaseName, ContainerName| 5 M| | Usado para monitorar a taxa de transferência provisionada por contêiner.|

### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (total) | Armazenamento total disponível relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Armazenamento disponível| Usada para monitorar a capacidade de armazenamento disponível (aplicável apenas para coleções de armazenamento fixas). A granularidade mínima deve ser de 5 minutos.| 
| Datautilization (uso de dados) |Bytes (total) |Uso total de dados relatados com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Tamanho dos dados | Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| IndexUsage (uso de índice) | Bytes (total) |Uso total do índice relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Tamanho do Índice| Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (cota de documentos) | Bytes (total) | Cota de armazenamento total relatada à granularidade de 5 minutos por região.| DatabaseName, CollectionName, Region| 5 M |Capacidade de Armazenamento| Usado para monitorar a cota total no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (total) |Contagem total de documentos relatados à granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Contagem de documentos|Usado para monitorar a contagem de documentos no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|

### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente| SourceRegion, TargetRegion| Todas | Usada para monitorar a latência de replicação P99 entre quaisquer duas regiões para uma conta com replicação geográfica. |


### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome para exibição da métrica) |Unidade (tipo de agregação)|Descrição| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---|
| Indisponibilidade (disponibilidade do serviço)| Percentual (mínimo, máximo) | Disponibilidade de solicitações de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a porcentagem do total de solicitações passadas. Uma solicitação será considerada com falha devido a erro de sistema se o código de status for 410, 500 ou 503. Usada para monitorar a disponibilidade da conta na granularidade de hora. |


### <a name="cassandra-api-metrics"></a>Métricas da API do Cassandra

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (solicitações de Cassandra) | Contagem (contagem) | Número de solicitações da API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Todas| Usada para monitorar solicitações do Cassandra em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60.|
| CassandraRequestCharges (encargos de solicitação do Cassandra) | Contagem (Sum, min, Max, AVG) | Unidades de solicitação consumidas pelas solicitações de API do Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todas| Usada para monitorar as RUs usadas por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (fechamentos de conexão do Cassandra) |Contagem (contagem) |Número de conexões do Cassandra fechadas| ClosureReason, Region| Todas | Usada para monitorar a conectividade entre os clientes e a API do Cassandra do Azure Cosmos DB.|

## <a name="next-steps"></a>Próximas etapas

* [Exibir e monitorar métricas de Azure Cosmos DB painel de métricas de conta](use-metrics.md)

* [Log de diagnóstico em Azure Cosmos DB](logging.md)
