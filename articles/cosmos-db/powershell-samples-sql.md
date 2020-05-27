---
title: Amostras do Azure PowerShell para o Azure Cosmos DB – API do SQL (Core)
description: Obtenha os exemplos de Azure PowerShell para executar várias tarefas comuns em contas da API do SQL do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 7cd14151d8cb0fbef7593d62eee050be7987ab18
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713532"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Amostras do Azure PowerShell para o Azure Cosmos DB – API do SQL (Core)

A tabela a seguir inclui links para scripts do Azure PowerShell comumente usados no Azure Cosmos DB para a API do SQL (Core). Caso deseje criar fork dessas amostras do PowerShell para o Cosmos DB em nosso repositório GitHub, visite [Amostras do PowerShell para o Cosmos DB no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Para obter mais amostras do PowerShell para o Cosmos DB para a API do SQL (Core) e a documentação, confira [Gerenciar recursos da API do SQL do Azure Cosmos DB usando o PowerShell](manage-with-powershell.md). Para obter amostras do PowerShell para o Cosmos DB para outras APIs, confira [API do Cassandra](powershell-samples-cassandra.md), [API do MongoDB](powershell-samples-mongodb.md), [API do Gremlin](powershell-samples-gremlin.md) e [API de Tabela](powershell-samples-table.md).

> [!NOTE]
> Os exemplos usam cmdlets de gerenciamento do [AZ.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Verifique regularmente se há atualizações ao `Az.CosmosDB`.

| | |
|---|---|
|[Criar uma conta, um banco de dados e um contêiner](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um contêiner do Azure Cosmos DB. |
|[Criar um contêiner com uma chave de partição grande](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um contêiner com uma chave de partição grande. |
|[Listar ou obter bancos de dados ou contêineres](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou contêineres. |
|[Obter RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as RU/s de um banco de dados ou um contêiner. |
|[Atualizar RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as RU/s para um banco de dados ou um contêiner. |
|[Criar um contêiner sem a política de índice](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma política de índice desativada.|
|[Atualizar uma conta](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta do Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar as regiões de uma conta do Cosmos DB. |
|[Alterar a prioridade de failover ou disparar um failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover regional de uma conta do Azure Cosmos ou dispare um failover manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cadeias de conexão ou regenere uma chave de conta de uma conta do Azure Cosmos DB. |
|[Criar uma conta do Cosmos com o firewall de IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta do Azure Cosmos DB com o firewall de IP habilitado. |
|||
