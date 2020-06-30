---
title: Amostras do Azure PowerShell para o Azure Cosmos DB – API do MongoDB
description: Obtenha os exemplos do Azure PowerShell para executar várias tarefas comuns na API do Azure Cosmos DB para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 368883a7eded17180a4a4259d452be09ebd221d9
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262439"
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
|[Bloquear recursos da exclusão](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
