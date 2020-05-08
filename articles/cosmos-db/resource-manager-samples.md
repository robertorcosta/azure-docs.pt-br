---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791622"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

As tabelas a seguir incluem links para modelos de Azure Resource Manager para Azure Cosmos DB:

## <a name="core-sql-api"></a>API do Core (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta do Azure Cosmos, banco de dados, contêiner com taxa de transferência de dimensionamento automático](manage-sql-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API de núcleo (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência de dimensionamento automático. |
|[Criar uma conta do Azure Cosmos, um banco de dados, um contêiner com taxa de transferência padrão (manual)](manage-sql-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API básica (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência padrão. |
|[Criar uma conta do Azure Cosmos, um banco de dados e um contêiner com um procedimento armazenado, um gatilho e um UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta de API básica (SQL) em duas regiões com um procedimento armazenado, um gatilho e um UDF para um contêiner. |
|[Criar um ponto de extremidade privado para uma conta existente do Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto de extremidade privado para uma conta de API do Azure Cosmos Core (SQL) existente em uma rede virtual existente. |
|[Criar uma conta de camada gratuita do Azure Cosmos](manage-sql-with-resource-manager.md#free-tier) |  Este modelo cria uma conta de API do Azure Cosmos DB Core (SQL) na camada gratuita. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, banco de dados, coleção com taxa de transferência de dimensionamento automático](manage-mongodb-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com dois contêineres que compartilham a taxa de transferência de dimensionamento automático no nível do banco de dados. |
|[Criar uma conta do Azure Cosmos, um banco de dados, uma coleção com taxa de transferência padrão (manual)](manage-mongodb-with-resource-manager.md#create-manual) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com dois contêineres que compartilham a taxa de transferência padrão no nível do banco de dados. |

## <a name="cassandra-api"></a>API Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, keyspace, tabela com taxa de transferência de dimensionamento automático](manage-cassandra-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API do Cassandra em duas regiões com um keyspace e uma tabela com taxa de transferência de dimensionamento automático. |
|[Criar uma conta do Azure Cosmos, keyspace, tabela com taxa de transferência padrão (manual)](manage-cassandra-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API do Cassandra em duas regiões com um keyspace e uma tabela com taxa de transferência manual. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, banco de dados, grafo com taxa de transferência de dimensionamento automático](manage-gremlin-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API do Gremlin em duas regiões com um banco de dados e um grafo com taxa de transferência de dimensionamento automático. |
|[Criar uma conta do Azure Cosmos, um banco de dados, um grafo com taxa de transferência padrão (manual)](manage-gremlin-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API do Gremlin em duas regiões com um banco de dados e um grafo com taxa de transferência padrão. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, tabela com taxa de transferência de dimensionamento automático](manage-table-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API de Tabela em duas regiões e uma única tabela com taxa de transferência de dimensionamento automático. |
|[Criar uma conta do Azure Cosmos, tabela com taxa de transferência padrão (manual)](manage-table-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API de Tabela em duas regiões e uma única tabela com taxa de transferência padrão. |

Consulte [Azure Resource Manager referência para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para obter a documentação de referência.
