---
title: Tutorial de distribuição global do Azure Cosmos DB para a API de Tabela
description: Saiba como a distribuição global funciona em contas da API de Tabela do Azure Cosmos DB e como configurar a lista de regiões preferida
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 148e17edbb8be566db611216f444fedad514e638
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770600"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB usando a API de Tabela

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Conectar-se a uma região preferencial usando a API de Tabela

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito configurando a propriedade [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). O SDK da API de Tabela do Azure Cosmos DB escolhe o melhor ponto de extremidade para se comunicar com base na configuração da conta, disponibilidade regional atual e na lista de preferência fornecida.

O PreferredLocations deve conter uma lista separada por vírgulas de locais (hospedagem múltipla) preferenciais para leituras. Cada instância do cliente pode especificar um subconjunto dessas regiões na ordem preferida para leituras de baixa latência. As regiões devem ser nomeadas usando seus [nomes de exibição](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`.

Todas as leituras são enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante.

Os SDKs tentam fazer a leitura de regiões especificadas na lista PreferredLocations. Desse modo, se a Conta do Banco de Dados estiver disponível em três regiões, por exemplo, mas o cliente especificar apenas duas das regiões de não gravação para PreferredLocations, nenhuma leitura será atendida fora da região de gravação, mesmo no caso de failover.

O SDK envia automaticamente todas as gravações para a região de gravação atual.

Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API de Tabela Azure Cosmos DB

