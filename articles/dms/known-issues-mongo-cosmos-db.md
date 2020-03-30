---
title: 'Problemas conhecidos: Migrar do MongoDB para o Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações do MongoDB para o Azure Cosmos DB usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256026"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Problemas/limitações de migração conhecidas com migrações do MongoDB para a API do Azure Cosmos DB para MongoDB

Problemas e limitações conhecidas associadas às migrações do MongoDB para a API do Cosmos DB para MongoDB são descritas nas seguintes seções.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>A migração falha como resultado do uso do SSL Cert incorreto

* **Sintoma**: Este problema é aparente quando um usuário não pode se conectar ao servidor de origem do MongoDB. Apesar de ter todas as portas de firewall abertas, o usuário ainda não consegue se conectar.

| Causa         | Resolução |
| ------------- | ------------- |
| O uso de um certificado auto-assinado no Azure Database Migration Service pode levar à falha na migração devido ao SSL Cert incorreto. A mensagem de erro pode incluir "O certificado remoto é inválido de acordo com o procedimento de validação." | Use um certificado genuíno da CA.  Certs auto-assinados são geralmente usados apenas em testes internos. Quando você instala um cert genuíno de uma autoridade ca, você pode usar o SSL no Azure Database Migration Service sem problemas (conexões ao Cosmos DB usam SSL sobre a API do Mongo).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Não é possível obter a lista de bancos de dados para mapear no DMS

* **Sintoma**: Não é possível obter a lista DB na lâmina **de configuração do banco** de dados ao usar dados do modo de armazenamento Do **Zure** na lâmina **de origem Select.**

| Causa         | Resolução |
| ------------- | ------------- |
| A seqüência de conexão da conta de armazenamento está faltando as informações do SAS e, portanto, não pode ser autenticada. | Crie o SAS no recipiente blob no Storage Explorer e use a URL com informações do SAS do contêiner como string de conexão de detalhes de origem.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Usando uma versão sem suporte do banco de dados

* **Sintoma**: A migração falha.

| Causa         | Resolução |
| ------------- | ------------- |
| Você tenta migrar para o Azure Cosmos DB a partir de uma versão sem suporte do MongoDB. | À medida que novas versões do MongoDB são lançadas, elas são testadas para garantir a compatibilidade com o Azure Database Migration Service, e o serviço está sendo atualizado periodicamente para aceitar as versões mais recentes. Se houver uma necessidade imediata de migração, como solução em solução, você pode exportar os bancos de dados/coleções para o Azure Storage e o ponto da origem para o dump resultante. Crie o SAS no recipiente blob no Storage Explorer e, em seguida, use a URL com informações do SAS do contêiner como a seqüência de conexão de detalhes de origem.<br><br> |

## <a name="next-steps"></a>Próximas etapas

* Veja o tutorial [Migrate MongoDB para a API do Azure Cosmos DB para MongoDB on-line usando DMS](tutorial-mongodb-cosmos-db-online.md).
* Veja o tutorial [Migrate MongoDB para a API do Azure Cosmos DB para MongoDB offline usando DMS](tutorial-mongodb-cosmos-db.md).