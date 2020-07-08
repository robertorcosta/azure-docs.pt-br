---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Use modelos do Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586143"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

As tabelas a seguir incluem links para modelos do Azure Resource Manager para o Azure Cosmos DB.

## <a name="core-sql-api"></a>API do Core (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar conta do Azure Cosmos, banco de dados e contêiner com taxa de transferência de dimensionamento automático](manage-sql-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API do Core (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência de dimensionamento automático. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com armazenamento analítico](manage-sql-with-resource-manager.md#create-analytical-store) | Este modelo cria uma conta de API do Core (SQL) em uma região com um contêiner configurado com o TTL analítico habilitado e a opção de usar taxa de transferência manual ou de dimensionamento ou automático. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com taxa de transferência padrão (manual)](manage-sql-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API do Core (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência padrão. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com procedimento armazenado, gatilho e UDF (função definida pelo usuário)](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta de API do Core (SQL) em duas regiões com um procedimento armazenado, um gatilho e uma UDF (função definida pelo usuário) para um contêiner. |
|[Criar um ponto de extremidade privado para uma conta já existente do Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto de extremidade privado para uma conta já existente da API do Core (SQL) do Azure Cosmos em uma rede virtual existente. |
|[Criar uma conta de camada gratuita do Azure Cosmos](manage-sql-with-resource-manager.md#free-tier) |  Este modelo cria uma conta de API do Core (SQL) do Azure Cosmos DB na camada gratuita. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar conta do Azure Cosmos, banco de dados e coleção com taxa de transferência de dimensionamento automático](manage-mongodb-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta usando a API do Azure Cosmos DB para MongoDB em duas regiões com dois contêineres que compartilham a taxa de transferência de dimensionamento automático no banco de dados. |
|[Criar conta do Azure Cosmos, banco de dados e coleção com taxa de transferência padrão (manual)](manage-mongodb-with-resource-manager.md#create-manual) | Este modelo cria uma conta usando a API do Azure Cosmos DB para MongoDB em duas regiões com dois contêineres que compartilham a taxa de transferência padrão no banco de dados. |

## <a name="cassandra-api"></a>API Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar conta do Azure Cosmos, keyspace e tabela com taxa de transferência de dimensionamento automático](manage-cassandra-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API do Cassandra em duas regiões, com keyspace e tabela com taxa de transferência de dimensionamento automático. |
|[Criar conta do Azure Cosmos, keyspace e tabela com taxa de transferência padrão (manual)](manage-cassandra-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API do Cassandra em duas regiões, com keyspace e tabela com taxa de transferência padrão (manual). |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar conta do Azure Cosmos, banco de dados e grafo com taxa de transferência de dimensionamento automático](manage-gremlin-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API do Gremlin em duas regiões, com banco de dados e grafo com taxa de transferência de dimensionamento automático. |
|[Criar conta do Azure Cosmos, banco de dados e grafo com taxa de transferência padrão (manual)](manage-gremlin-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API do Gremlin em duas regiões, com banco de dados e grafo com taxa de transferência padrão. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar conta do Azure Cosmos e tabela com taxa de transferência de dimensionamento automático](manage-table-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta de API de Tabela em duas regiões e uma única tabela com taxa de transferência de dimensionamento automático. |
|[Criar conta do Azure Cosmos e tabela com taxa de transferência padrão (manual)](manage-table-with-resource-manager.md#create-manual) | Este modelo cria uma conta de API de Tabela em duas regiões e uma única tabela com taxa de transferência padrão. |

Confira a página [Referência do Azure Resource Manager para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) da documentação de referência.
