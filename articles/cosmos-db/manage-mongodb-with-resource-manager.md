---
title: Modelos do Resource Manager para API de Azure Cosmos DB para MongoDB
description: Use modelos de Azure Resource Manager para criar e configurar a API de Azure Cosmos DB para MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d6f916002f949f78e4854903940f342261a109ff
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791214"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB recursos de API do MongoDB usando modelos de Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos de Azure Resource Manager para ajudar a implantar e gerenciar suas contas de Azure Cosmos DB para a API, os bancos de dados e as coleções do MongoDB.

Este artigo tem exemplos para a API de Azure Cosmos DB somente para MongoDB, para encontrar exemplos para outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API de Azure Cosmos DB para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), artigos de [tabela](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizado.
> * Ao adicionar ou remover locais de uma conta do Azure Cosmos, você não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos de Azure Cosmos DB abaixo, copie o modelo de exemplo a seguir em um novo arquivo JSON. Opcionalmente, você pode criar um arquivo JSON de parâmetros para usar ao implantar várias instâncias do mesmo recurso com nomes e valores diferentes. Há várias maneiras de implantar modelos de Azure Resource Manager, incluindo, [portal do Azure](../azure-resource-manager/templates/deploy-portal.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Conta do Azure Cosmos para MongoDB com taxa de transferência provisionada de dimensionamento automático

Este modelo criará uma conta do Azure Cosmos para a API do MongoDB (3,2 ou 3,6) com duas coleções que compartilham a taxa de transferência de dimensionamento automático no nível do banco de dados. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Conta do Azure Cosmos para MongoDB com taxa de transferência provisionada padrão

Este modelo criará uma conta do Azure Cosmos para a API do MongoDB (3,2 ou 3,6) com duas coleções que compartilham a taxa de transferência padrão de 400 RU/s (manual) no nível do banco de dados. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
