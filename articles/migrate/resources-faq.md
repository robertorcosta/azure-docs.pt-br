---
title: Perguntas frequentes sobre migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o serviço migrações para Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81530310"
---
# <a name="azure-migrate-common-questions"></a>Migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre as migrações para Azure. Se você tiver dúvidas depois de ler este artigo, poderá lançá-los no [Fórum migrações para Azure](https://aka.ms/AzureMigrateForum). Você também pode examinar estes artigos:

- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>O que são as Migrações para Azure?

As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem pública e privada para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração e ofertas de ISV de terceiros. [Saiba mais](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com as migrações para Azure?

Use as migrações para Azure para descobrir, avaliar e migrar dados, aplicativos e infraestrutura locais para o Azure. As migrações para Azure dão suporte à avaliação e migração de VMs VMware locais, VMs do Hyper-V, servidores físicos, outras VMs virtualizadas, bancos de dados, aplicativos Web e áreas de trabalho virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre migrações e Azure Site Recovery do Azure?

As [migrações para Azure](migrate-services-overview.md) fornecem um hub centralizado para avaliação e migração para o Azure. 

[Azure site Recovery](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastre. 

A ferramenta migrações para Azure: Server Migration usa algumas funcionalidades de Site Recovery de back-end para a migração de deslocamento e mudança de algumas máquinas locais.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre as migrações para Azure: avaliação de servidor e o MAP Toolkit?

A avaliação do servidor fornece avaliação para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. O [Kit de ferramentas de avaliação e planejamento (MAP) da Microsoft](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planejamento de migração para versões mais recentes de sistemas operacionais de cliente e servidor do Windows e acompanhamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o Site Recovery Planejador de Implantações?

A avaliação do servidor é uma ferramenta de planejamento de migração. O Site Recovery Planejador de Implantações é uma ferramenta de planejamento de recuperação de desastre.

Escolha sua ferramenta com base no que você deseja fazer:

- **Planejar a migração local para o Azure**: se você planeja migrar seus servidores locais para o Azure, use a avaliação do servidor para planejamento de migração. A avaliação do servidor avalia as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você poderá usar ferramentas como migrações para Azure: migração de servidor para migrar os computadores para o Azure.
- **Planejar a recuperação de desastre para o Azure**: se você planeja configurar a recuperação de desastre do local para o azure com site Recovery, use o Planejador de Implantações de site Recovery. O Planejador de Implantações fornece uma avaliação profunda e específica Site Recovery do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações relacionadas à recuperação de desastre, como replicação e failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração de servidor com o Site Recovery?

- Se você usar as migrações para Azure: migração de servidor para executar uma migração *sem agente* de VMs VMware locais, a migração será nativa para migrações para Azure e site Recovery não será usada.
- Se você usar migrações para Azure: migração de servidor para executar uma migração *baseada em agente* de VMs VMware ou se migrar VMs Hyper-V ou servidores físicos, migrações para Azure: a migração de servidor usa o mecanismo de replicação Azure site Recovery.

## <a name="which-geographies-are-supported"></a>Quais regiões têm suporte?

Examine as geografias com suporte para [nuvens](migrate-support-matrix.md#supported-geographies-azure-government) [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e governamentais.

## <a name="how-do-i-get-started"></a>Como começar?

Identifique a ferramenta de que você precisa e, em seguida, adicione a ferramenta a um projeto de migrações para Azure. 

Para adicionar uma ferramenta ISV ou um movimentador:

1. Comece obtendo uma licença ou Inscreva-se para uma avaliação gratuita, de acordo com a política de ferramentas. O licenciamento de ferramentas está de acordo com o modelo de licenciamento de ferramentas ou de ISV.
2. Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure.

Você pode acompanhar sua jornada de migração de dentro do projeto de migrações para Azure, entre o Azure e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto?

Saiba como [excluir um projeto](how-to-delete-project.md). 

## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
