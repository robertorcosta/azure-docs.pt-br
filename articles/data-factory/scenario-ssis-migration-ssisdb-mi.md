---
title: Migração do SSIS com a instância gerenciada do Azure SQL Database como destino da carga de trabalho do banco de dados
description: A migração do SSIS com o Banco de Dados SQL do Azure foi gerenciada como o destino da carga de trabalho do banco de dados.
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
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419044"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migração do SSIS com a instância gerenciada do Azure SQL Database como destino da carga de trabalho do banco de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de banco de dados do SQL Server para a instância gerenciada do Azure SQL Database, você deve estar familiarizado com o [DMS (Azure Data Migration Service, serviço](https://docs.microsoft.com/azure/dms/dms-overview)de migração de dados de dados do Azure) e as [topologias de rede para migrações de instâncias gerenciadas do Azure SQL Database usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Este artigo se concentra na migração de pacotes SSIS (SSIS) do SQL Server Integration Service (SSIS) armazenados no catálogo SSIS (SSISDB) e no SQL Server Agent que agendam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrar catálogo SSIS (SSISDB)

A migração do SSISDB pode ser feita usando o DMS, conforme descrito no artigo: [Migrar pacotes SSIS para uma instância gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Empregos ssis para azure SQL Database agente de instância gerenciada

A instância gerenciada do Banco de Dados SQL do Azure tem um programador nativo de primeira classe, assim como o SQL Server Agent no local.  Uma vez que uma ferramenta de migração para trabalhos SSIS ainda não está disponível, eles têm que ser migrados do SQL Server Agent no local para o agente de instância gerenciado do Azure SQL Database via scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Integration Runtime do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topoologias de rede para migrações de instânciagerenciadas do Azure SQL Database usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes SSIS para uma instância gerenciada do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SSISDB no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
