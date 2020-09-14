---
title: Saiba como mover uma conta de Azure Cosmos DB para outra região
description: Saiba como mover uma conta de Azure Cosmos DB para outra região
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059153"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Como mover uma conta de Azure Cosmos DB para outra região

Este artigo descreve como mover uma região onde os dados são replicados em Azure Cosmos DB ou como migrar os metadados da conta (Azure Resource Manager), bem como os dados de uma região para outra.

## <a name="move-data-from-one-region-to-another"></a>Mover dados de uma região para outra

O Azure Cosmos DB dá suporte à replicação de dados nativamente, de modo que mover dados de uma região para outra é simples e pode ser feito usando o portal do Azure, Azure PowerShell ou CLI do Azure e envolve as seguintes etapas:

1. Adicionar uma nova região à conta

    Para adicionar uma nova região a uma conta de Azure Cosmos DB, consulte [Adicionar/remover regiões para uma conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Executar um failover manual para a nova região

    Em situações em que a região que está sendo removida é atualmente a região de gravação para a conta, você precisará iniciar um failover para a nova região adicionada acima. Essa é uma operação sem tempo de inatividade. Se você estiver movendo uma região de leitura em uma conta de várias regiões, poderá ignorar esta etapa. Para iniciar um failover, consulte [executar failover manual em uma conta do Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Remover a região original

    Para remover uma região de uma conta de Azure Cosmos DB, consulte [Adicionar/remover regiões para uma conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrar metadados de conta de Azure Cosmos DB

O Azure Cosmos DB não dá suporte nativo à migração de metadados de conta de uma região para outra. Para migrar os metadados da conta e os dados do cliente de uma região para outra, uma nova conta deve ser criada na região desejada e, em seguida, os dados devem ser copiados manualmente. Uma migração de tempo de inatividade quase zero para a API do SQL requer o uso de [ChangeFeed](change-feed.md) ou uma ferramenta que o utiliza. Se estiver migrando a API do MongoDB, Cassandra ou outra API ou para saber mais sobre as opções ao migrar dados entre contas, consulte [opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB](cosmosdb-migrationchoices.md). As etapas a seguir demonstram como migrar uma conta de Azure Cosmos DB para a API do SQL e seus dados de uma região para outra:

1. Criar uma nova conta de Azure Cosmos DB na região desejada

    Para criar uma nova conta por meio do portal do Azure, PowerShell ou CLI, consulte [criar uma conta de Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Criar um novo banco de dados e contêiner

    Para criar um novo banco de dados e contêiner, consulte [criar um contêiner Cosmos do Azure](how-to-create-container.md)

1. Migrar dados com a ferramenta Azure Cosmos DB Live Data Migrator

    Para migrar dados com tempo de inatividade quase zero, consulte [Azure Cosmos DB ferramenta do Live Data Migrator](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Atualizar cadeia de conexão do aplicativo

    Com a ferramenta do Live Migrator ainda em execução, atualize as informações de conexão na nova implantação de seus aplicativos. Os pontos de extremidade e as chaves do seu aplicativo podem ser recuperados do portal do Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL":::

1. Redirecionar solicitações para o novo aplicativo

    Depois que o novo aplicativo estiver conectado a Azure Cosmos DB você poderá redirecionar solicitações de cliente para sua nova implantação.

1. Excluir todos os recursos que não são mais necessários

    Com as solicitações agora redirecionadas completamente para a nova instância, você pode excluir a conta de Azure Cosmos DB antiga e a ferramenta do Live Data Migrator.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como o banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Alterar feed no Azure Cosmos DB](change-feed.md)
