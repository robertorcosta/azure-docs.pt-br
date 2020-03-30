---
title: Etapas de otimização pós-migração com a API do Azure Cosmos DB para MongoDB
description: Este doc fornece as técnicas de otimização pós-migração do MongoDB para a APi do Azure Cosmos DB para O Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063608"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de otimização pós-migração ao usar a API do Azure Cosmos DB para MongoDB

Depois de migrar os dados armazenados no banco de dados do MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar ao Azure Cosmos DB e gerenciar os dados. Este guia fornece as etapas que você deve considerar após a migração. Consulte o [tutorial Migrar MongoDB para API do Azure Cosmos DB para mongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as etapas de migração.

Neste guia, você aprenderá a:

- [Conecte seu aplicativo](#connect-your-application)
- [Otimizar a política de indexação](#optimize-the-indexing-policy)
- [Configure a distribuição global da API do Azure Cosmos DB para MongoDB](#globally-distribute-your-data)
- [Definir nível de consistência](#set-consistency-level)

> [!NOTE]
> A única etapa obrigatória pós-migração no nível do aplicativo é alterar a seqüência de conexões em seu aplicativo para apontar para sua nova conta Azure Cosmos DB. Todas as outras etapas de migração são otimizações recomendadas.
>

## <a name="connect-your-application"></a>Conecte seu aplicativo

1. Em um novo sinal de janela no [portal Azure](https://www.portal.azure.com/)
2. A partir do [portal Azure,](https://www.portal.azure.com/)no painel esquerdo abra o menu **Todos os recursos** e encontre a conta Azure Cosmos DB para a qual você migrou seus dados.
3. Abra a **lâmina da corda de conexão.** O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.
4. Use as informações de conexão na configuração do seu aplicativo (ou em outros lugares relevantes) para refletir a API do Azure Cosmos DB para conexão MongoDB em seu aplicativo.
![String de conexão](./media/mongodb-post-migration/connection-string.png)

Para obter mais detalhes, consulte o [aplicativo Conecte um MongoDB na página do Azure Cosmos DB.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Otimizar a política de indexação

Todos os campos de dados são automaticamente indexados, por padrão, durante a migração de dados para o Azure Cosmos DB. Em muitos casos, essa política de indexação padrão é aceitável. Em geral, a remoção de índices otimiza as solicitações de gravação e ter a política de indexação padrão (ou seja, indexação automática) otimiza as solicitações de leitura.

Para obter mais informações sobre indexação, consulte [Indexação de dados na API do Azure Cosmos DB para MongoDB,](mongodb-indexing.md) bem como na Indexação em artigos [do Azure Cosmos DB.](index-overview.md)

## <a name="globally-distribute-your-data"></a>Distribua globalmente seus dados

O Azure Cosmos DB está disponível em todas as [regiões do Azure](https://azure.microsoft.com/regions/#services) pelo mundo. Depois de selecionar o nível de consistência padrão para sua conta Azure Cosmos DB, você pode associar uma ou mais regiões do Azure (dependendo das suas necessidades globais de distribuição). Para alta disponibilidade e continuidade de negócios, recomendamos sempre correr em pelo menos 2 regiões. Você pode rever as dicas para otimizar o [custo de implantações multi-regiões no Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir globalmente seus dados, consulte [Distribuir dados globalmente na API do Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Definir nível de consistência

O Azure Cosmos DB oferece 5 níveis de [consistência](consistency-levels.md)bem definidos. Para ler sobre o mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB, leia [os níveis de consistência e APIs do Azure Cosmos DB](consistency-levels-across-apis.md). O nível de consistência padrão é o nível de consistência da sessão. Alterar o nível de consistência é opcional e você pode otimizá-lo para o seu aplicativo. Para alterar o nível de consistência usando o portal Azure:

1. Vá para a **lâmina consistência padrão** em Configurações.
2. Selecione seu [nível de consistência](consistency-levels.md)

A maioria dos usuários deixa seu nível de consistência na configuração padrão de consistência da sessão. No entanto, [existem compensações de disponibilidade e desempenho para vários níveis de consistência.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>Próximas etapas

* [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Conecte-se à conta azure Cosmos DB usando o Studio 3T](mongodb-mongochef.md)
* [Como configurar distribuir globalmente as leituras usando a API do Azure Cosmos DB para MongoDB](mongodb-readpreference.md)
* [Expirar os dados com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de solicitação no Azure Cosmos DB](request-units.md)