---
title: Etapas de pré-migração para migração de dados para a API do Azure Cosmos DB para MongoDB
description: Este doc fornece uma visão geral dos pré-requisitos para uma migração de dados do MongoDB para o Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942087"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para migrações de dados do MongoDB para a API do Azure Cosmos DB para MongoDB

Antes de migrar seus dados do MongoDB (no local ou na nuvem) para a API do Azure Cosmos DB para o MongoDB, você deve:

1. [Leia as principais considerações sobre o uso da API do Azure Cosmos DB para o MongoDB](#considerations)
2. [Escolha uma opção para migrar seus dados](#options)
3. [Estime o throughput necessário para suas cargas de trabalho](#estimate-throughput)
4. [Escolha uma chave de partição ideal para seus dados](#partitioning)
5. [Entenda a política de indexação que você pode definir em seus dados](#indexing)

Se você já tiver concluído os pré-requisitos acima para migração, você pode [migrar os dados do MongoDB para a API do Azure Cosmos DB para O MongoDB usando o Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db.md). Além disso, se você não criou uma conta, você pode navegar por qualquer uma das [Quickstarts](create-mongodb-dotnet.md) que mostram os passos para criar uma conta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerações ao usar a API do Azure Cosmos DB para MongoDB

A seguir estão características específicas sobre a API do Azure Cosmos DB para MongoDB:

- **Modelo de capacidade**: A capacidade do banco de dados no Azure Cosmos DB é baseada em um modelo baseado em throughput. Este modelo é baseado em [Unidades de Solicitação por segundo,](request-units.md)que é uma unidade que representa o número de operações de banco de dados que podem ser executadas contra uma coleta em uma base por segundo. Essa capacidade pode ser alocada em [um banco de dados ou nível de coleta,](set-throughput.md)e pode ser provisionada em um modelo de alocação, ou usando o modelo [AutoPilot](provision-throughput-autopilot.md).

- **Unidades de solicitação**: Toda operação de banco de dados tem um custo de RUs (Request Units, unidades de solicitação associada) no Azure Cosmos DB. Quando executado, este é subtraído do nível de unidades de solicitação disponíveis em um determinado segundo. Se uma solicitação exigir mais RUs do que o RU/s atualmente alocado, há duas opções para resolver o problema - aumentar a quantidade de RUs, ou esperar até que o próximo segundo comece e, em seguida, tentar novamente a operação.

- **Capacidade elástica**: A capacidade de uma determinada coleta ou banco de dados pode mudar a qualquer momento. Isso permite que o banco de dados se adapte elelásicamente aos requisitos de throughput da sua carga de trabalho.

- **Fragmento automático**: O Azure Cosmos DB fornece um sistema de particionamento automático que requer apenas um fragmento (ou uma chave de partição). O [mecanismo de particionamento automático](partition-data.md) é compartilhado em todas as APIs do Azure Cosmos DB e permite dados perfeitos e durante todo o dimensionamento através da distribuição horizontal.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opções de migração para a API do Azure Cosmos DB para MongoDB

O [API do Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornece um mecanismo que simplifica a migração de dados, fornecendo uma plataforma de hospedagem totalmente gerenciada, opções de monitoramento de migração e manipulação automática de estrangulamento. A lista completa de opções é a seguinte:

|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados.|
|Offline|[Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executores em massa Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;A falta de verificação significa que qualquer problema durante o curso da migração exigiria uma reinicialização de todo o processo de migração<br/>&bull;A falta de uma fila de letras mortas significaria que alguns arquivos errôneos poderiam parar todo o processo de migração <br/>&bull;Precisa de código personalizado para aumentar a leitura de throughput para determinadas fontes de dados|
|Offline|[Ferramentas Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Fácil de configurar e integração <br/>&bull;Precisa de manuseio personalizado para aceleradores|
|Online|[Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Serviço de migração totalmente gerenciado.<br/>&bull;Fornece soluções de hospedagem e monitoramento para a tarefa de migração. <br/>&bull;Adequado para grandes conjuntos de dados e cuida de replicar mudanças ao vivo <br/>&bull;Funciona apenas com outras fontes do MongoDB|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Estime a necessidade de throughput para suas cargas de trabalho

No Azure Cosmos DB, o throughput é provisionado com antecedência e é medido em Unidades de Solicitação (RU's) por segundo. Ao contrário de VMs ou servidores locais, as RUs são fáceis de escalar a qualquer momento. Você pode alterar o número de RUs provisionados instantaneamente. Para obter mais informações, confira [Unidades de solicitação no Azure Cosmos DB](request-units.md).

Você pode usar a Calculadora de [Capacidade do Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para determinar a quantidade de Unidades de Solicitação com base na configuração da sua conta de banco de dados, quantidade de dados, tamanho do documento e leituras e gravações necessárias por segundo.

A seguir, os principais fatores que afetam o número de RUs requeridas:
- **Tamanho**do documento : À medida que o tamanho de um item/documento aumenta, o número de RUs consumidos para ler ou escrever o item/documento também aumenta.

- **Contagem de propriedades do documento**:O número de RUs consumidos para criar ou atualizar um documento está relacionado ao número, complexidade e comprimento de suas propriedades. Você pode reduzir o consumo da unidade de solicitação para operações de gravação [limitando o número de propriedades indexadas](mongodb-indexing.md).

- **Padrões de consulta**: A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas pela consulta. 

A melhor maneira de entender o custo das consultas é usar dados de amostra no Azure Cosmos DB [e executar consultas de exemplo do MongoDB Shell](connect-mongodb-account.md) usando o `getLastRequestStastistics` comando para obter a taxa de solicitação, que irá produzir o número de RUs consumidos:

`db.runCommand({getLastRequestStatistics: 1})`

Este comando produzirá um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Você também pode usar [as configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) para entender a frequência e os padrões das consultas executadas contra o Azure Cosmos DB. Os resultados dos registros de diagnóstico podem ser enviados para uma conta de armazenamento, uma instância eventHub ou [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Escolha sua chave de partição
Particionamento, também conhecido como Sharding, é um ponto-chave de consideração antes de migrar dados. O Azure Cosmos DB usa particionamento totalmente gerenciado para aumentar a capacidade em um banco de dados para atender aos requisitos de armazenamento e throughput. Esse recurso não precisa da hospedagem ou configuração dos servidores de roteamento.   

Da mesma forma, o recurso de particionamento adiciona automaticamente capacidade e reequilibra os dados de acordo. Para obter detalhes e recomendações sobre a escolha da chave de partição certa para seus dados, consulte o [artigo Escolher uma chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexar os dados
Por padrão, o Azure Cosmos DB fornece indexação automática em todos os dados inseridos. Os recursos de indexação fornecidos pelo Azure Cosmos DB incluem a adição de índices compostos, índices únicos e índices de tempo de vida (TTL). A interface de gerenciamento de `createIndex()` índices é mapeada para o comando. Saiba mais na [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md).

[O Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migra automaticamente as coleções do MongoDB com índices exclusivos. No entanto, os índices únicos devem ser criados antes da migração. O Azure Cosmos DB não suporta a criação de índices únicos, quando já há dados em suas coleções. Para obter mais informações, veja [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Próximas etapas
* [Migre seus dados do MongoDB para o Cosmos DB usando o Serviço de Migração de Banco de Dados.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos](set-throughput.md)
* [Particionamento no BD Cosmos do Azure](partition-data.md)
* [Distribuição Global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de solicitação no Azure Cosmos DB](request-units.md)
