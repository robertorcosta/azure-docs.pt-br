---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Use os modelos do Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961850"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

As tabelas a seguir incluem links para modelos do Azure Resource Manager para O Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---|---|
|[Crie uma conta, um banco de dados e um contêiner do Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta API SQL (Core) em duas regiões com dois contêineres com throughput de banco de dados compartilhado e um contêiner com throughput dedicado. O throughput pode ser atualizado reenviando o modelo com valor de propriedade de throughput atualizado. |
|[Crie uma conta, banco de dados e contêiner do Azure Cosmos com um procedimento armazenado, gatilho e UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta API SQL (Core) em duas regiões com um procedimento armazenado, gatilho e UDF para um contêiner. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Crie uma conta do Azure Cosmos, banco de dados, coleta](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta usando a API DB do Azure Cosmos para O MongoDB em duas regiões com habilitação multimestre. A conta do Azure Cosmos terá dois contêineres que compartilham o throughput em nível de banco de dados. |

## <a name="cassandra-api"></a>API Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Crie uma conta do Azure Cosmos, keyspace, tabela](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta API Cassandra em duas regiões com habilitação multi-mestre. A conta do Azure Cosmos terá duas tabelas que compartilham o throughput de nível de espaço-chave. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Crie uma conta do Azure Cosmos, banco de dados, gráfico](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta API Gremlin em duas regiões com habilitação multimestre. A conta do Azure Cosmos terá dois gráficos que compartilham o throughput em nível de banco de dados. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Crie uma conta do Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta De API de tabela em duas regiões com habilitação multimestre. A conta do Azure Cosmos terá uma única tabela. |

> [!TIP]
> Para habilitar o throughput compartilhado ao usar a API da tabela, habilite o throughput em nível de conta no Portal Azure.

Consulte [a referência do Azure Resource Manager para a página do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) para obter a documentação de referência.
