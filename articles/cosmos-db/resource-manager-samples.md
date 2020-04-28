---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193251"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

As tabelas a seguir incluem links para modelos de Azure Resource Manager para Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---|---|
|[Crie uma conta, um banco de dados e um contêiner do Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com dois contêineres com taxa de transferência de banco de dados compartilhada e um contêiner com taxa de transferência dedicada. A taxa de transferência pode ser atualizada reenviando o modelo com o valor da propriedade de produtividade atualizado. |
|[Criar uma conta do Azure Cosmos, um banco de dados e um contêiner com um procedimento armazenado, um gatilho e um UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com um procedimento armazenado, Trigger e UDF para um contêiner. |
|[Criar um ponto de extremidade privado para uma conta existente do Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto de extremidade privado para uma conta existente da API do SQL do Azure Cosmos em uma rede virtual existente. |
|[Criar uma conta de camada gratuita do Azure Cosmos](manage-sql-with-resource-manager.md#free-tier) |  Este modelo cria uma conta de Azure Cosmos DB na camada gratuita. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, banco de dados, coleção](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois contêineres que compartilham a taxa de transferência no nível do banco de dados. |

## <a name="cassandra-api"></a>API Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, keyspace, tabela](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Cassandra em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá duas tabelas que compartilham a taxa de transferência de nível de espaço. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, um banco de dados, um grafo](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Gremlin em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois grafos que compartilham a taxa de transferência no nível do banco de dados. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API de Tabela em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá uma única tabela. |

> [!TIP]
> Para habilitar a taxa de transferência compartilhada ao usar API de Tabela, habilite a taxa de transferência no nível da conta no portal do Azure.

Consulte [Azure Resource Manager referência para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para obter a documentação de referência.
