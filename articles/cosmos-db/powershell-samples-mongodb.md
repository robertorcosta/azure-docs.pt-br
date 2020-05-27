---
title: Amostras do Azure PowerShell para o Azure Cosmos DB – API do MongoDB
description: Obtenha os exemplos do Azure PowerShell para executar várias tarefas comuns na API do Azure Cosmos DB para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: c8e0a7a60a3512d19a1dfdfdb07b20e523ce7b92
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649721"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Amostras do Azure PowerShell para o Azure Cosmos DB – API do MongoDB

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Azure Cosmos DB para a API do MongoDB.

> [!NOTE]
> No momento, você só pode criar a versão 3.2 (ou seja, contas que usam o ponto de extremidade no formato `*.documents.azure.com`) da API do Azure Cosmos DB para as contas do MongoDB usando o PowerShell, a CLI e os modelos do Resource Manager. Para criar a versão 3.6 das contas, use portal do Azure.

> [!NOTE]
> Os exemplos usam cmdlets de gerenciamento do [AZ.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Verifique regularmente se há atualizações ao `Az.CosmosDB`.

| | |
|---|---|
|[Criar uma conta, um banco de dados e uma coleção](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um banco de dados e uma coleção do Azure Cosmos. |
|[Listar ou obter bancos de dados ou coleções](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou uma coleção. |
|[Obter RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as RU/s de um banco de dados ou uma coleção. |
|[Atualizar RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as RU/s para um banco de dados ou uma coleção. |
|[Atualizar uma conta ou adicionar uma região](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adicione uma região a uma conta do Cosmos. Também pode ser usado para modificar outras propriedades da conta, mas essas alterações precisam ser separadas das alterações nas regiões. |
|[Alterar a prioridade de failover ou disparar um failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover regional de uma conta do Azure Cosmos ou dispare um failover manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cadeias de conexão ou regenere uma chave de conta de uma conta do Azure Cosmos. |
|[Criar uma conta do Cosmos com o firewall de IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta do Azure Cosmos com o firewall de IP habilitado. |
|||
