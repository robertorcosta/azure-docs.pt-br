---
title: Série migratória Contoso | Microsoft Docs
description: Links para Contoso exemplo cenários de migração, para migração para o Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676320"
---
# <a name="contoso-migration-series"></a>Série de migração da Contoso


Temos uma série de artigos que demonstram como a organização fictícia Contoso migra sua infra-estrutura local para a nuvem [do Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

A série inclui cenários que ilustram como configurar uma migração de infra-estrutura e como executar diferentes tipos de migrações. Os cenários crescem em complexidade à medida que avançam. Os artigos mostram como a empresa Contoso lida com a migração, mas instruções gerais e ponteiros são fornecidos por toda parte.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela a seguir.  

- Cada cenário de migração é impulsionado por requisitos de negócios ligeiramente diferentes, que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre drivers e metas de negócios, uma arquitetura proposta, etapas para realizar a migração e recomendações para limpeza e próximos passos após a conclusão da migração.


**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1: Visão geral](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. 
[Artigo 2: Implantar a infraestrutura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A Contoso prepara sua infra-estrutura local e a infra-estrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos da série. 
[Artigo 3: Avaliar recursos locais para migração para o Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados.
[Artigo 4: Hospedar novamente um aplicativo em uma Instância Gerenciada do Banco de Dados SQL e VM do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. Contoso migra o aplicativo front-end VM usando [o Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migra o banco de dados do aplicativo para uma instância gerenciada do Banco de Dados SQL do Azure, usando o Serviço de Migração de Banco de Dados do [Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A Contoso migra suas VMs do aplicativo SmartHotel360 para as VMs do Azure usando o serviço Azure Migrate. 
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | A Contoso migra o aplicativo SmartHotel360. Contoso usa o Azure Migrate para migrar o aplicativo VMs. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. 
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso completa uma migração de elevador e mudança de seu aplicativo Linux osTicket para VMs do Azure, usando o Azure Migrate.
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migra seu aplicativo Linux osTicket para VMs do Azure usando o Azure Migrate. Ele migra o banco de dados do aplicativo para o Azure Database for MySQ, usando o Azure Database Migration Service (inclui uma opção alternativa usando o MySQL Workbench).
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A Contoso migra seu aplicativo SmartHotel360 para um aplicativo web do Azure e migra o banco de dados do aplicativo para uma instância do Azure SQL Server, usando o Serviço de Migração de Banco de Dados do Azure.
[Artigo 10: Refatorar um aplicativo do Windows usando o Azure App Services e a Instância Gerenciada sql](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migra um aplicativo baseado no Windows para um aplicativo web do Azure e migra o banco de dados do aplicativo para uma instância gerenciada do Azure SQL, usando o Serviço de Migração de Banco de Dados do Azure.
[Artigo 11: Refatorie um aplicativo Linux em um aplicativo web Azure e Banco de Dados Azure para MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migra seu aplicativo Linux osTicket para um aplicativo web Do Zure em várias regiões do Azure, usando o Azure Traffic Manager, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. 
[Artigo 12: Servidor da Fundação Refactor Team nos serviços do Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure.
[Artigo 13: Recompilar um aplicativo no Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A Contoso reconstrói seu aplicativo SmartHotel usando uma série de recursos e serviços do Azure, incluindo O Serviço de Aplicativos Azure, Azure Kubernetes Service (AKS), Funções Azure, Azure Cognitive Services e Azure Cosmos DB.
[Artigo 14: Escalonar uma migração para o Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migração de nuvens.
- Conheça as estratégias de migração para outros cenários (pares de origem/destino) no [Guia de Migração](https://datamigration.microsoft.com/)de Banco de Dados .
