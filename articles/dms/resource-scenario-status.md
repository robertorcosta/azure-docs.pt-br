---
title: Status do cenário de migração de banco de dados
titleSuffix: Azure Database Migration Service
description: Conheça o status dos cenários de migração suportados pelo Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254923"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status dos cenários de migração suportados pelo Azure Database Migration Service

O Azure Database Migration Service foi projetado para suportar diferentes cenários de migração (pares de origem/destino) para migrações off-line (one-time) e on-line (sincronização contínua). A cobertura de cenário fornecida pelo Azure Database Migration Service está sendo estendida ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica cenários de migração atualmente suportados pelo Azure Database Migration Service e o status (visualização privada, visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Migrações offline versus online

Com o Azure Database Migration Service, você pode fazer uma migração offline ou on-line. Nas migrações *offline*, o tempo de inatividade do aplicativo começa quando a migração inicia. Para limitar o tempo de inatividade ao tempo necessário para cortar para o novo ambiente quando a migração for concluída, use uma migração *on-line.* Recomenda-se testar uma migração offline para determinar se o tempo de inatividade é aceitável; se não, faça uma migração online.

## <a name="migration-scenario-status"></a>Status do cenário de migração

O status dos cenários de migração suportados pelo Azure Database Migration Service varia de acordo com o tempo. Geralmente, os cenários são lançados pela primeira vez em **pré-visualização privada.** Participar de uma pré-visualização privada exige que os clientes enviem uma nomeação através do [site de visualização do DMS](https://aka.ms/dms-preview). Após a visualização privada, o status do cenário muda para **visualização pública**. Os usuários do Azure Database Migration Service podem testar cenários de migração em visualização pública diretamente da interface do usuário. Não é necessário fazer inscrição.  No entanto, os cenários de migração na pré-visualização pública podem não estar disponíveis em todas as regiões e podem sofrer alterações adicionais antes do lançamento final. Após a visualização pública, o status do cenário muda para **disponibilidade geral**. Disponibilidade geral (GA) é o status final de lançamento, e a funcionalidade é completa e acessível a todos os usuários.

## <a name="migration-scenario-support"></a>Suporte para o cenário de migração

As tabelas a seguir mostram quais cenários de migração são suportados ao usar o Azure Database Migration Service.

> [!NOTE]
> Se um cenário listado como suportado abaixo não aparecer na interface do usuário, entre em contato com o alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para visualizar todos os cenários atualmente suportados pelo Azure Database Migration Service in Private Preview, consulte o [site Depré-visualização do DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte para a migração offline (única)

A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações offline.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS |  |  |
|   | Oracle |  |  |
| **SQL do Azure para MI do BD** | SQL Server | ✔ | GA |
|   | SQL para RDS |  |  |
|   | Oracle |  |   |
| **VM do SQL do Azure** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL |   |   |
|   | MySQL para RDS |   |   |
| **Banco de dados do Azure para PostgreSQL** | PostgreSQL |  |
|  | PostgreSQL para RDS |   |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronização contínua)

A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações online.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS | ✔ | GA |
|   | Oracle |  |  |
| **SQL do Azure para MI do BD** | SQL Server | ✔ | GA |
|   | SQL para RDS | ✔ | GA |
|   | Oracle | ✔ | Visualização particular |
| **VM do SQL do Azure** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL | ✔ | GA |
|   | MySQL para RDS | ✔ | GA |
| **Banco de dados do Azure para PostgreSQL** | PostgreSQL | ✔ | GA |
|   | PostgreSQL para RDS | ✔ | GA |
|   | Oracle | ✔ | Versão prévia pública |

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [What is the Azure Database Migration Service](dms-overview.md).
