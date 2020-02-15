---
title: O que é o Serviço de Migração de Banco de Dados do Azure?
description: Visão geral do serviço de migração de banco de dados do Azure, que fornece migrações diretas de várias fontes de banco de dado para as plataformas do Azure data
services: database-migration
author: pochiraju
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 06/01/2019
ms.openlocfilehash: d573378bc5e729eb75b6c3b51d3671492f7f98f1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209368"
---
# <a name="what-is-azure-database-migration-service"></a>O que é o Serviço de Migração de Banco de Dados do Azure?

O serviço de migração de banco de dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações diretas de várias fontes de banco de dados para as plataformas do Azure com tempo de inatividade mínimo (migrações online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrar bancos de dados para Azure com ferramentas clássicas

O serviço de migração de banco de dados do Azure integra algumas das funcionalidades de nossas ferramentas e serviços existentes. Ele fornece aos clientes uma solução abrangente e altamente disponível. O serviço usa o [Data Migration Assistant](https://aka.ms/dma) para gerar relatórios de avaliação que fornecem recomendações para orientar você durante as alterações necessárias antes de executar uma migração. Cabe a você executar qualquer correção necessária. Quando você estiver pronto para iniciar o processo de migração, o serviço de migração de banco de dados do Azure executará todas as etapas necessárias. Você pode acioná-lo e ficar tranquilo quanto aos seus projetos de migração, com a certeza de que o processo aproveita as melhores práticas determinadas pela Microsoft.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

## <a name="regional-availability"></a>Disponibilidade regional

Para obter informações atualizadas sobre a disponibilidade regional do serviço de migração de banco de dados do Azure, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Preços

Para obter informações atualizadas sobre os preços do serviço de migração de banco de dados do Azure, consulte [preços do serviço de migração de banco de dados do Azure](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Próximas etapas

* [Status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure](resource-scenario-status.md).
* [Crie uma instância do serviço de migração de banco de dados do Azure usando o portal do Azure](quickstart-create-data-migration-service-portal.md).
* [Migre do SQL Server para o Banco de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md).
* [Visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure](pre-reqs.md).
* [FAQ sobre como usar o serviço de migração de banco de dados do Azure](faq.md).
* [Serviços e ferramentas disponíveis para cenários de migração de dados](dms-tools-matrix.md).
