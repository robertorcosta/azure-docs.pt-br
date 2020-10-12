---
title: Série de migração da contoso | Microsoft Docs
description: Links para cenários de migração de exemplo da Contoso, para migração para o Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107624"
---
# <a name="contoso-migration-series"></a>Série de migração da Contoso


Temos uma série de artigos que demonstram como a organização fictícia Contoso migra sua infraestrutura local para a nuvem de [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

A série inclui cenários que ilustram como configurar uma migração de infraestrutura e como executar diferentes tipos de migrações. Os cenários crescem em complexidade à medida que eles avançam. Os artigos mostram como a empresa contoso lida com a migração, mas instruções e ponteiros gerais são fornecidos em todo o.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela a seguir.  

- Cada cenário de migração é orientado por requisitos de negócios ligeiramente diferentes, que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre objetivos e drivers de negócios, uma arquitetura proposta, etapas para executar a migração e recomendações para limpeza e próximas etapas após a conclusão da migração.


**Artigo** | **Detalhes** 
--- | --- 
[Artigo 1: Visão geral](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. 
[Artigo 2: Implantar a infraestrutura do Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | A contoso prepara sua infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos da série. 
[Artigo 3: Avaliar recursos locais para migração para o Azure](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados.
[Artigo 4: rehospedar um aplicativo em uma VM do Azure e no SQL Instância Gerenciada](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A contoso migra a VM de front-end do aplicativo usando as [migrações para Azure](./migrate-services-overview.md). A contoso migra o banco de dados de aplicativo para um SQL Instância Gerenciada, usando o [serviço de migração de banco de dados do Azure](../dms/dms-overview.md).
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | A contoso migra suas VMs de aplicativo SmartHotel360 para VMs do Azure usando o serviço migrações para Azure. 
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | A Contoso migra o aplicativo SmartHotel360. A contoso usa as migrações para Azure para migrar as VMs de aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. 
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | A contoso conclui uma migração de deslocamento e mudança de seu aplicativo osTicket do Linux para VMs do Azure, usando migrações para Azure.
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e Banco de Dados do Azure para MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | A contoso migra seu aplicativo osTicket do Linux para VMs do Azure usando migrações para Azure. Ele migra o banco de dados de aplicativo para o banco de dados do Azure para MySQ usando o serviço de migração de banco de dados do Azure (inclui uma opção alternativa usando o MySQL Workbench).
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e Banco de Dados SQL do Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | A contoso migra seu aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para o banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.
[Artigo 10: refatorar um aplicativo do Windows usando os serviços Azure App e o SQL Instância Gerenciada](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | A contoso migra um aplicativo baseado no Windows local para um aplicativo Web do Azure e migra o banco de dados de aplicativo para um Azure SQL Instância Gerenciada usando o serviço de migração de banco de dados do Azure.
[Artigo 11: refatorar um aplicativo do Linux em um aplicativo Web do Azure e banco de dados do Azure para MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | A contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure, usando o Gerenciador de tráfego do Azure, integrado com o GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. 
[Artigo 12: Team Foundation Server de refatoração em Azure DevOps Services](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure.
[Artigo 13: Recompilar um aplicativo no Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | A contoso recompila seu aplicativo SmartHotel usando uma variedade de recursos e serviços do Azure, incluindo serviço de Azure App, AKS (serviço kubernetes do Azure), Azure Functions, serviços cognitivas do Azure e Azure Cosmos DB.
[Artigo 14: Escalonar uma migração para o Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre](/azure/architecture/cloud-adoption/migrate/) a migração na nuvem.
- Saiba mais sobre estratégias de migração para outros cenários (pares de origem/destino) no [Guia de migração de banco de dados](https://datamigration.microsoft.com/).
