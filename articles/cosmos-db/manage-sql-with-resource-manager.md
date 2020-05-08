---
title: Criar e gerenciar Azure Cosmos DB com modelos do Resource Manager
description: Usar modelos de Azure Resource Manager para criar e configurar a API do Azure Cosmos DB para núcleo (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 577bc34e5e4b01a234460e5e175c23fd8215743f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791197"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gerenciar recursos de API do Azure Cosmos DB Core (SQL) com modelos de Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos de Azure Resource Manager para ajudar a implantar e gerenciar suas contas, bancos de dados e contêineres do Azure Cosmos DB.

Este artigo mostra apenas Azure Resource Manager exemplos de modelo para contas de API de núcleo (SQL). Você também pode encontrar exemplos de modelo para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e APIs de [tabela](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizado.
> * Ao adicionar ou remover locais de uma conta do Azure Cosmos, você não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos de Azure Cosmos DB abaixo, copie o modelo de exemplo a seguir em um novo arquivo JSON. Opcionalmente, você pode criar um arquivo JSON de parâmetros para usar ao implantar várias instâncias do mesmo recurso com nomes e valores diferentes. Há várias maneiras de implantar modelos de Azure Resource Manager, incluindo, [portal do Azure](../azure-resource-manager/templates/deploy-portal.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Conta do Azure cosmos com taxa de transferência de dimensionamento automático

Este modelo cria uma conta do Azure Cosmos em duas regiões com opções de consistência e failover, com Banco de dados e contêiner configurados para taxa de transferência de dimensionamento automático que tem a maioria das opções de política habilitada. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>Conta do Azure cosmos com taxa de transferência padrão (manual)

Este modelo cria uma conta do Azure Cosmos em duas regiões com opções de consistência e failover, com o banco de dados e o contêiner configurados para a taxa de transferência padrão que tem a maioria das opções de política habilitada. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB contêiner com funcionalidade do lado do servidor

Este modelo cria uma conta do Azure Cosmos, um banco de dados e um contêiner com um procedimento armazenado, um gatilho e uma função definida pelo usuário. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Conta de Azure Cosmos DB de camada gratuita

Este modelo cria uma conta de camada gratuita do Azure Cosmos e um banco de dados com taxa de transferência compartilhada que pode ser compartilhada com até 25 contêineres. Esse modelo também está disponível para implantação de um clique na Galeria de modelos de início rápido do Azure.

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
