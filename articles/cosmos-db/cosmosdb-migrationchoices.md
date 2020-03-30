---
title: Opções de migração do Cosmos DB
description: Este doc descreve as várias opções para migrar seus dados on-premises ou cloud para o Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72882396"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opções para migrar seus dados on-premises ou cloud para o Azure Cosmos DB

Você pode carregar dados de várias fontes de dados para o Azure Cosmos DB. Além disso, como o Azure Cosmos DB suporta várias APIs, os alvos podem ser qualquer uma das APIs existentes. A fim de suportar caminhos de migração das várias fontes para as diferentes APIs do Azure Cosmos DB, existem várias soluções que fornecem manuseio especializado para cada caminho de migração. Este documento lista as soluções disponíveis e descreve suas vantagens e limitações.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fatores que afetam a escolha da ferramenta de migração

Os seguintes fatores determinam a escolha da ferramenta de migração:
* **Migração on-line vs off-line**: Muitas ferramentas de migração fornecem um caminho para fazer uma migração única apenas. Isso significa que os aplicativos que acessam o banco de dados podem sofrer um período de inatividade. Algumas soluções de migração fornecem uma maneira de fazer uma migração ao vivo onde há um pipeline de replicação configurado entre a fonte e o alvo.

* **Fonte de dados**: Os dados existentes podem estar em várias fontes de dados como Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, etc. Os dados também podem estar em uma conta Azure Cosmos DB existente e a intenção de migração pode ser alterar o modelo de dados ou reparticionar os dados em um contêiner com uma chave de partição diferente.

* **Azure Cosmos DB API**: Para a API SQL no Azure Cosmos DB, há uma variedade de ferramentas desenvolvidas pela equipe do Azure Cosmos DB que ajudam nos diferentes cenários de migração. Todas as outras APIs possuem seu próprio conjunto especializado de ferramentas desenvolvidas e mantidas pela comunidade. Uma vez que o Azure Cosmos DB suporta essas APIs em um nível de protocolo de fio, essas ferramentas devem funcionar como-é ao migrar dados para o Azure Cosmos DB também. No entanto, eles podem exigir manuseio personalizado para aceleradores, já que este conceito é específico para o Azure Cosmos DB.

* **Tamanho dos dados**: A maioria das ferramentas de migração funcionam muito bem para conjuntos de dados menores. Quando o conjunto de dados excede algumas centenas de gigabytes, as opções de ferramentas de migração são limitadas. 

* **Duração esperada de migração**: As migrações podem ser configuradas para ocorrer em um ritmo lento e incremental que consome menos throughput ou pode consumir todo o throughput provisionado no contêiner Azure Cosmos DB de destino e concluir a migração em menos tempo.

## <a name="azure-cosmos-db-sql-api"></a>API do SQL do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados|
|Offline|[Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executores em massa Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;Falta de verificação - Significa que se um problema ocorrer durante o curso da migração, você precisa reiniciar todo o processo de migração<br/>&bull;Falta de uma fila de letras mortas - Isso significa que alguns arquivos errados podem parar todo o processo de migração.|
|Offline|[Conector Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Faz uso da biblioteca de executores em massa Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;Precisa de uma configuração personalizada do Spark <br/>&bull;Faísca é sensível a inconsistências de esquema e isso pode ser um problema durante a migração |
|Offline|[Ferramenta personalizada com biblioteca de executores em massa Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Fornece recursos de checkpoint e letras mortas que aumentam a resiliência da migração <br/>&bull;Adequado para conjuntos de dados muito grandes (10 TB+)  <br/>&bull;Requer configuração personalizada desta ferramenta em execução como um serviço de aplicativo |
|Online|[Funções do Cosmos DB + API do ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Fácil de configurar <br/>&bull;Funciona apenas se a fonte for um contêiner Azure Cosmos DB <br/>&bull;Não é adequado para grandes conjuntos de dados <br/>&bull;Não captura exclusões do recipiente de origem |
|Online|[Serviço de migração personalizado usando o ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull;Fornece rastreamento de progresso <br/>&bull;Funciona apenas se a fonte for um contêiner Azure Cosmos DB <br/>&bull;Funciona para conjuntos de dados maiores também <br/>&bull;Requer que o usuário configure um serviço de aplicativo para hospedar o processador de feed Change <br/>&bull;Não captura exclusões do recipiente de origem|
|Online|[Rio Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server <br/>&bull;Fácil de construir gasodutos ETL e fornece um painel para monitoramento <br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Como se trata de uma ferramenta de terceiros, ela precisa ser comprada no marketplace e instalada no ambiente do usuário|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados|
|Offline|[Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executores em massa Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;A falta de verificação significa que qualquer problema durante o curso da migração exigiria uma reinicialização de todo o processo de migração<br/>&bull;A falta de uma fila de letras mortas significaria que alguns arquivos errôneos poderiam parar todo o processo de migração <br/>&bull;Precisa de código personalizado para aumentar a leitura de throughput para determinadas fontes de dados|
|Offline|[Ferramentas Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Fácil de configurar e integração <br/>&bull;Precisa de manuseio personalizado para aceleradores|
|Online|[Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Faz uso da biblioteca de executores em massa Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados e cuida de replicar mudanças ao vivo <br/>&bull;Funciona apenas com outras fontes do MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API do Cassandra do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[comando cqlsh COPY](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Fácil de configurar <br/>&bull;Não é adequado para grandes conjuntos de dados <br/>&bull;Funciona apenas quando a fonte é uma mesa Cassandra|
|Offline|[Tabela de cópia com Faísca](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Pode fazer uso de recursos de Faísca para paralelelizar transformação e ingestão <br/>&bull;Precisa de configuração com uma política de repetição personalizada para lidar com aceleradores|
|Online|[Striim (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server <br/>&bull;Fácil de construir gasodutos ETL e fornece um painel para monitoramento <br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Como se trata de uma ferramenta de terceiros, ela precisa ser comprada no marketplace e instalada no ambiente do usuário|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Como se trata de uma ferramenta de terceiros, ela precisa ser comprada no marketplace e instalada no ambiente do usuário|

## <a name="other-apis"></a>Outras APIs
Para APIs além da API SQL, API mongo e a API Cassandra, existem várias ferramentas apoiadas por cada um dos ecossistemas existentes da API. 

**API de tabela** 
* [Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [Biblioteca de executores em massa de gráficos](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Centelha Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais experimentando os aplicativos de amostra que consomem a biblioteca de executores em massa em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* A biblioteca executora em massa é integrada ao conector Cosmos DB Spark, para saber mais, consulte o artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* Entre em contato com a equipe de produtos do Azure Cosmos DB abrindo um ticket de suporte o tipo de problema General Advisory e subtipo de problemas Grandes (TB+) para obter ajuda adicional com migrações em larga escala.
