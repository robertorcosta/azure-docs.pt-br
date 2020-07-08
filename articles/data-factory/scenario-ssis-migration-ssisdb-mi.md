---
title: Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados
description: Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116303"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de banco de dados de uma instância SQL Server para o SQL do Azure Instância Gerenciada, você deve estar familiarizado com o [serviço de migração do Azure data](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) e as [topologias de rede para SQL instância gerenciada migrações usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Este artigo se concentra na migração de pacotes do SSIS (serviço de integração do SQL Server) armazenados no catálogo do SSIS (SSISDB) e em trabalhos de SQL Server Agent que agendam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrar catálogo do SSIS (SSISDB)

A migração do SSISDB pode ser feita usando DMS, conforme descrito no artigo: [migrar pacotes do SSIS para o SQL instância gerenciada](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Trabalhos do SSIS para o SQL Instância Gerenciada Agent

O SQL Instância Gerenciada tem um Agendador nativo de primeira classe, assim como SQL Server Agent local.  Como uma ferramenta de migração para trabalhos do SSIS ainda não está disponível, eles precisam ser migrados do SQL Server Agent local para o SQL Instância Gerenciada Agent por meio de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Integration Runtime do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologias de rede para migrações do SQL Instância Gerenciada usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes do SSIS para um SQL Instância Gerenciada](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SSISDB no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes do SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
