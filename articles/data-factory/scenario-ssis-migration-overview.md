---
title: Migrar cargas de trabalho ssis no local para SSIS na fábrica de dados do Azure
description: Migre as cargas de trabalho do SSIS no local para o SSIS no ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929801"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrar cargas de trabalho ssis no local para SSIS no ADF

## <a name="overview"></a>Visão geral

Quando você migra suas cargas de trabalho de banco de dados do SQL Server para os serviços de banco de dados do Azure, ou seja, o Banco de Dados SQL do Azure ou a instância gerenciada do Banco de Dados SQL Do Azure, suas cargas de trabalho ETL no SSIS (SSIS) como um dos principais serviços de integração de valor agregado os serviços também precisarão ser migrados.

O Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF) suporta a execução de pacotes SSIS. Uma vez que o Azure-SSIS IR seja provisionado, você pode usar ferramentas familiares, como sql server data tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de comando, como dtinstall/dtutil/dtexec, para implantar e executar seus pacotes no Azure. Para obter mais informações, consulte [a visão geral do azure SSIS lift-and-shift](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Este artigo destaca o processo de migração de suas cargas de trabalho ETL de SSIS no local para SSIS no ADF. O processo de migração consiste em duas fases: **Avaliação** e **Migração.**

## <a name="assessment"></a>Avaliação

Para estabelecer um plano de migração completo, uma avaliação completa ajudará a identificar problemas com os pacotes SSIS de origem que impediriam uma migração bem-sucedida.

O DMA (Data Migration Assistant, assistente de migração de dados) é uma ferramenta livremente baixada para este fim que pode ser instalada e executada localmente. O projeto de avaliação dma dos serviços de **integração** do tipo pode ser criado para avaliar pacotes SSIS em lotes e identificar problemas de compatibilidade que são apresentados nas seguintes categorias:

- Bloqueadores de migração: são problemas de compatibilidade que bloqueiam os pacotes de origem de migração para serem executados no IR Azure-SSIS. O DMA fornece orientações para ajudá-lo a resolver esses problemas.

- Problemas informativos: São recursos parcialmente suportados ou depreciados que são usados em pacotes de origem. O DMA fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e medidas mitigadoras para resolver.

### <a name="four-storage-types-for-ssis-packages"></a>Quatro tipos de armazenamento para pacotes SSIS

- Catálogo SSIS (SSISDB). Isso foi introduzido com o SQL Server 2012 e contém um conjunto de procedimentos armazenados, visualizações e funções de valor de tabela usadas para trabalhar com projetos/pacotes SSIS.
- Sistema de arquivos.
- Banco de dados do sistema SQL Server (MSDB).
- Loja de pacotes SSIS. Esta é uma camada de gerenciamento de pacotes em cima de dois subtipos:
  - MSDB, que é um banco de dados de sistema no SQL Server usado para armazenar pacotes SSIS.
  - Sistema de arquivos gerenciado, que é uma pasta específica no caminho de instalação do SQL Server usado para armazenar pacotes SSIS.

Atualmente, a DMA suporta a avaliação em lote de pacotes armazenados no sistema de **arquivos,** **na loja de pacotes**e **no catálogo SSIS** desde a **versão DMA v5.0**.

Obtenha [DMA](https://docs.microsoft.com/sql/dma/dma-overview)e [realize sua avaliação de pacote com ele.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>Migração

Dependendo dos tipos de [armazenamento](#four-storage-types-for-ssis-packages) dos pacotes SSIS de origem e do destino migratório das cargas de trabalho do banco de dados, as etapas para migrar **pacotes SSIS** e **trabalhos de Agente de Servidor SQL** que agendam execuções de pacotes SSIS podem variar. Há dois cenários:

- [**Azure SQL Database gerenciado instância** como destino de carga de trabalho de banco de dados](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Banco de dados SQL do Azure** como destino de carga de trabalho de banco de dados](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database gerenciado instância** como destino de carga de trabalho de banco de dados

| **Tipo de armazenamento de pacotes** |Como migrar em lote pacotes SSIS|Como migrar em lote os empregos do SSIS|
|-|-|-|
|SSISDB|[Migrar **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrar empregos SSIS para o agente de instância gerenciada do Banco de Dados SQL do Azure](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Sistema de Arquivos|Reimplantá-los para arquivos de arquivos/Arquivos Azure através de dtinstall/dtutil/manual copy, ou para manter em sistemas de arquivos para acessar via VNet/IR auto-hospedado. Para obter mais informações, consulte [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Servidor SQL (MSDB)|Exporte-os para sistemas de arquivos/compartilhamentos de arquivos/Arquivos Azure via SSMS/dtutil. Para obter mais informações, consulte [exportar pacotes SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Repositório de pacotes|Exporte-os para arquivos/compartilhamentos de arquivos/Arquivos Azure via SSMS/dtutil ou reimplantá-los para arquivos de arquivos/Arquivos Azure via dtinstall/dtutil/manual copy ou mantê-los em sistemas de arquivos para acessar via VNet/IR auto-hospedado. Para obter mais informações, consulte dtutil utility. Para obter mais informações, consulte [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Banco de dados SQL do Azure** como destino de carga de trabalho de banco de dados

| **Tipo de armazenamento de pacotes** |Como migrar em lote pacotes SSIS|Como migrar em lote|
|-|-|-|
|SSISDB|Reimplante no Azure-SSISDB via SSDT/SSMS. Para obter mais informações, consulte [Implantando pacotes SSIS no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Arquivos|Reimplantá-los para arquivos de arquivos/Arquivos Azure através de dtinstall/dtutil/manual copy, ou para manter em sistemas de arquivos para acessar via VNet/IR auto-hospedado. Para obter mais informações, consulte [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Servidor SQL (MSDB)|Exporte-os para sistemas de arquivos/compartilhamentos de arquivos/Arquivos Azure via SSMS/dtutil. Para obter mais informações, consulte [exportar pacotes SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Repositório de pacotes|Exporte-os para arquivos/compartilhamentos de arquivos/Arquivos Azure via SSMS/dtutil ou reimplantá-los para arquivos de arquivos/Arquivos Azure via dtinstall/dtutil/manual copy ou mantê-los em sistemas de arquivos para acessar via VNet/IR auto-hospedado. Para obter mais informações, consulte dtutil utility. Para obter mais informações, consulte [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em pipelines/atividades/gatilhos adnatos do ADF através do portal Scripts/SSMS/ADF. Para obter mais informações, consulte [o recurso de agendamento do SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Recursos adicionais

- [Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Assistente de Migração de Banco de Dados](https://docs.microsoft.com/sql/dma/dma-overview)
- [Levante e mude as cargas de trabalho do SSIS para a nuvem](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrar os pacotes SSIS para uma instância gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Reimplantar pacotes no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Próximas etapas

- [Validar pacotes do SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Executar pacotes SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitore o tempo de execução da integração Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Agende execuções de pacotes SSIS no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
