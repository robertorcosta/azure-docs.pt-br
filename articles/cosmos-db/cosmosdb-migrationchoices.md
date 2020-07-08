---
title: Opções de migração de Cosmos DB
description: Este documento descreve as várias opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: a1b8ddba84920d8d3b6871ab404081d3b24c72e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261963"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB

Você pode carregar dados de várias fontes de dados para Azure Cosmos DB. Além disso, como Azure Cosmos DB dá suporte a várias APIs, os destinos podem ser qualquer uma das APIs existentes. Para oferecer suporte a caminhos de migração de várias fontes para as diferentes APIs de Azure Cosmos DB, há várias soluções que fornecem tratamento especializado para cada caminho de migração. Este documento lista as soluções disponíveis e descreve suas vantagens e limitações.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fatores que afetam a escolha da ferramenta de migração

Os seguintes fatores determinam a escolha da ferramenta de migração:
* **Migração online vs offline**: muitas ferramentas de migração fornecem um caminho para fazer uma única migração única. Isso significa que os aplicativos que acessam o banco de dados podem apresentar um período de tempo de inatividade. Algumas soluções de migração fornecem uma maneira de fazer uma migração ao vivo em que há um pipeline de replicação configurado entre a origem e o destino.

* **Fonte de dados**: os dados existentes podem estar em várias fontes de dados, como Oracle DB2, Datastax Cassanda, banco de dados SQL do Azure, PostgreSQL, etc. Os dados também podem estar em uma conta de Azure Cosmos DB existente e a intenção de migração pode ser alterar o modelo de dados ou reparticionar os dados em um contêiner com uma chave de partição diferente.

* **API de Azure Cosmos DB**: para a API do SQL no Azure Cosmos DB, há uma variedade de ferramentas desenvolvidas pela equipe de Azure Cosmos DB que auxiliam nos diferentes cenários de migração. Todas as outras APIs têm seu próprio conjunto especializado de ferramentas desenvolvidas e mantidas pela Comunidade. Como o Azure Cosmos DB dá suporte a essas APIs em um nível de protocolo de conexão, essas ferramentas devem funcionar como estão enquanto migram dados para o Azure Cosmos DB também. No entanto, eles podem exigir manipulação personalizada para restrições, pois esse conceito é específico para Azure Cosmos DB.

* **Tamanho dos dados**: a maioria das ferramentas de migração funciona muito bem para conjuntos menores. Quando o conjunto de dados exceder algumas centenas de gigabytes, as opções de ferramentas de migração são limitadas. 

* **Duração de migração esperada**: as migrações podem ser configuradas para serem realizadas em um ritmo lento e incremental que consome menos taxa de transferência ou pode consumir toda a taxa de transferência provisionada no contêiner de Azure Cosmos DB de destino e concluir a migração em menos tempo.

## <a name="azure-cosmos-db-sql-api"></a>API do SQL do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de altos|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; Fácil de configurar e suporta várias fontes <br/>&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequada para grandes conjuntos de dados <br/>&bull;Falta de ponto de verificação-isso significa que, se ocorrer um problema durante a migração, você precisará reiniciar todo o processo de migração<br/>&bull;Falta de uma fila de mensagens mortas-isso significa que alguns arquivos errados podem parar todo o processo de migração.|
|Offline|[Azure Cosmos DB conector do Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequada para grandes conjuntos de dados <br/>&bull;Precisa de uma instalação personalizada do Spark <br/>&bull;O Spark é sensível a inconsistências de esquema e pode ser um problema durante a migração |
|Offline|[Ferramenta personalizada com Cosmos DB biblioteca de executores em massa](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Fornece recursos de ponto de verificação e mensagens mortas que aumentam a resiliência da migração <br/>&bull;Adequado para conjuntos de grandes volumes (10 TB +)  <br/>&bull;Requer a instalação personalizada dessa ferramenta em execução como um serviço de aplicativo |
|Online|[Funções de Cosmos DB + API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Fácil de configurar <br/>&bull;Funciona somente se a origem for um contêiner Azure Cosmos DB <br/>&bull;Não é adequado para grandes conjuntos de altos <br/>&bull;Não captura exclusões do contêiner de origem |
|Online|[Serviço de migração personalizado usando ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Fornece acompanhamento de progresso <br/>&bull;Funciona somente se a origem for um contêiner Azure Cosmos DB <br/>&bull;Também funciona para conjuntos de valores maiores <br/>&bull;Requer que o usuário configure um serviço de aplicativo para hospedar o processador do feed de alterações <br/>&bull;Não captura exclusões do contêiner de origem|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes, como Oracle, DB2 SQL Server <br/>&bull;É fácil criar pipelines ETL e fornece um painel para monitoramento <br/>&bull;Dá suporte a conjuntos de grandes maiores <br/>&bull;Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário|

## <a name="azure-cosmos-db-mongo-api"></a>API do Mongo Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de altos|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; Fácil de configurar e suporta várias fontes <br/>&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequada para grandes conjuntos de dados <br/>&bull; A falta de pontos de verificação significa que qualquer problema durante o curso da migração exigiria uma reinicialização de todo o processo de migração<br/>&bull; A falta de uma fila de mensagens mortas significa que alguns arquivos errados poderiam parar todo o processo de migração <br/>&bull; Precisa de código personalizado para aumentar a taxa de transferência de leitura para determinadas fontes de dados|
|Offline|[Ferramentas do Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Fácil de configurar e de integrar <br/>&bull; Precisa de manipulação personalizada para limitações|
|Online|[Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; Usa a biblioteca de executores em massa do Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados e cuida da replicação de alterações dinâmicas <br/>&bull; Funciona somente com outras fontes do MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API do Cassandra do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[comando de cópia cqlsh](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Fácil de configurar <br/>&bull;Não é adequado para grandes conjuntos de altos <br/>&bull;Funciona somente quando a origem é uma tabela Cassandra|
|Offline|[Copiar tabela com Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Pode usar os recursos do Spark para paralelizar a transformação e a ingestão <br/>&bull;Precisa de configuração com uma política de repetição personalizada para manipular limitadores|
|Online|[Striim (de Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes, como Oracle, DB2 SQL Server <br/>&bull;É fácil criar pipelines ETL e fornece um painel para monitoramento <br/>&bull;Dá suporte a conjuntos de grandes maiores <br/>&bull;Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário|
|Online|[Blitzz (de Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Dá suporte a conjuntos de grandes maiores <br/>&bull;Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário|

## <a name="other-apis"></a>Outras APIs
Para APIs que não sejam a API do SQL, a API Mongo e a API do Cassandra, há várias ferramentas com suporte em cada um dos ecossistemas existentes da API. 

**API de Tabela** 
* [Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**API do Gremlin**
* [Biblioteca de executor em massa do grafo](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais experimentando os aplicativos de exemplo que consomem a biblioteca de executores em massa no [.net](bulk-executor-dot-net.md) e no [Java](bulk-executor-java.md). 
* A biblioteca de executores em massa é integrada ao conector do Cosmos DB Spark, para saber mais, consulte o artigo [Azure Cosmos DB conector do Spark](spark-connector.md) .  
* Entre em contato com a equipe de produto Azure Cosmos DB abrindo um tíquete de suporte no tipo de problema "consultoria geral" e no subtipo de problema "migrações grandes (TB +)" para obter ajuda adicional com migrações em larga escala.
