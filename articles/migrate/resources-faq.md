---
title: Azure Migrar FAQ
description: Obtenha respostas para perguntas comuns sobre o serviço Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530310"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: Perguntas comuns

Este artigo responde a perguntas comuns sobre o Azure Migrate. Se você tiver dúvidas depois de ler este artigo, você pode postá-las no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Você também pode rever esses artigos:

- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>O que são as Migrações para Azure?

O Azure Migrate fornece um hub central para rastrear a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho no local e VMs de nuvem privada e pública para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração e ofertas de ISV de terceiros. [Saiba mais](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com o Azure Migrate?

Use o Azure Migrate para descobrir, avaliar e migrar a infra-estrutura, aplicativos e dados no local para o Azure. O Azure Migrate suporta avaliação e migração de VMS VMware, VMs Hyper-V, servidores físicos, outras VMs virtualizadas, bancos de dados, aplicativos web e desktops virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre o Azure Migrate e o Azure Site Recovery?

[O Azure Migrate](migrate-services-overview.md) fornece um hub centralizado para avaliação e migração para o Azure. 

[A recuperação do site do Azure](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastres. 

A ferramenta Azure Migrate: Server Migration usa algumas funcionalidades de recuperação de site back-end para migração de elevador e mudança de algumas máquinas locais.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre o Azure Migrate: Server Assessment e o MAP Toolkit?

A Avaliação do Servidor fornece avaliação para ajudar na prontidão da migração e avaliação de cargas de trabalho para migração para o Azure. O [Kit de Ferramentas de Avaliação e Planejamento da Microsoft (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planejamento de migração para versões mais recentes de sistemas operacionais de clientes e servidores do Windows e o rastreamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o planejador de implantação de recuperação de site?

A Avaliação do Servidor é uma ferramenta de planejamento de migração. O Site Recovery Deployment Planner é uma ferramenta de planejamento de recuperação de desastres.

Escolha sua ferramenta com base no que você deseja fazer:

- **Planeje a migração no local para o Azure**: Se você planeja migrar seus servidores locais para o Azure, use a Avaliação do Servidor para o planejamento de migração. A Avaliação do Servidor avalia as cargas de trabalho no local e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você pode usar ferramentas como o Azure Migrate: Migração de servidor para migrar as máquinas para o Azure.
- **Planeje a recuperação de desastres para o Azure**: Se você planeja configurar a recuperação de desastres no local para o Azure com a Recuperação do Site, use o Planejador de Implantação de Recuperação do Site. O Planejador de Implantação fornece uma avaliação profunda e específica da recuperação do local do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações relacionadas à recuperação de desastres, como replicação e failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração do servidor com a recuperação do site?

- Se você usar o Azure Migrate: Migração de servidor para executar uma migração *sem agente* de VMMs VMware no local, a migração será nativa do Azure Migrate e a Recuperação do Site não será usada.
- Se você usar o Azure Migrate: Migração de servidor para executar uma migração baseada em agente de VMMs *baseado em agentes* ou se você migrar VMs hyper-V ou servidores físicos, o Azure Migrate: Server Migration usa o mecanismo de replicação do Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Quais geografias são apoiadas?

Reveja as geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="how-do-i-get-started"></a>Como começar?

Identifique a ferramenta necessária e adicione a ferramenta a um projeto do Azure Migrate. 

Para adicionar uma ferramenta ISV ou Movere:

1. Comece obtendo uma licença ou inscreva-se para uma avaliação gratuita, de acordo com a política da ferramenta. O licenciamento de ferramentas está de acordo com o modelo de licenciamento de ferramentas ou de ISV.
2. Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e documentação da ferramenta para conectar a ferramenta com o Azure Migrate.

Você pode acompanhar sua jornada de migração de dentro do projeto Azure Migrate, através do Azure e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como excluir um projeto?

Aprenda a [excluir um projeto](how-to-delete-project.md). 

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
