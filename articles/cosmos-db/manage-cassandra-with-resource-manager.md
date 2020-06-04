---
title: Modelos do Resource Manager para a API do Cassandra do Azure Cosmos DB
description: Use modelos do Azure Resource Manager para criar e configurar a API do Cassandra do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 630c965eadf17e21c75e1bd180fec4496df830e2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684832"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerenciar recursos da API do Cassandra do Azure Cosmos DB usando modelos do Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos do Azure Resource Manager para ajudar a implantar e gerenciar contas, keyspacess e tabelas do Azure Cosmos DB.

Este artigo tem exemplos apenas das contas de API do Cassandra. Para encontrar exemplos de outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API do Azure Cosmos DB para artigos do [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Tabela](manage-table-with-resource-manager.md).

> [!IMPORTANT]
>
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizadas.
> * Quando você adiciona ou remove locais de uma conta do Azure Cosmos, não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o modelo de exemplo a seguir em um novo arquivo JSON. Opcionalmente, você pode criar um arquivo JSON de parâmetros para usar ao implantar várias instâncias do mesmo recurso com nomes e valores diferentes. Há várias maneiras de implantar modelos do Azure Resource Manager, incluindo [portal do Azure](../azure-resource-manager/templates/deploy-portal.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Conta do Azure Cosmos para Cassandra com taxa de transferência provisionada de dimensionamento automático

Este modelo cria uma conta do Azure Cosmos em duas regiões, com opções de consistência e failover, com um keyspace e uma tabela configurada para a taxa de transferência de dimensionamento automático. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Conta do Azure Cosmos para Cassandra com taxa de transferência provisionada padrão

Este modelo cria uma conta do Azure Cosmos em duas regiões, com opções de consistência e failover, com um keyspace e tabela configurados para taxa de transferência padrão. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema de provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido do Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Solução de erros comuns de implantação do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
