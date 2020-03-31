---
title: Avalie um grande número de servidores físicos para migração para o Azure com o Azure Migrate | Microsoft Docs
description: Descreve como avaliar um grande número de servidores físicos para migração para o Azure usando o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294364"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Avalie um grande número de servidores físicos para migração para o Azure

Este artigo descreve como avaliar um grande número de servidores físicos locais para migração para o Azure, usando a ferramenta Azure Migrate Server Assessment.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 


Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure as permissões do Azure e prepare servidores físicos para avaliação.
> * Crie um projeto do Azure Migrate e crie uma avaliação.
> * Revise a avaliação como você planeja para a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga nossa [série de tutoriais](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planejar a avaliação de um grande número de servidores físicos, há algumas coisas para pensar:

- **Planos Projetos do Azure Migrate**: Descubra como implantar projetos do Azure Migrate. Por exemplo, se seus data centers estão em diferentes geografias ou você precisa armazenar metadados relacionados à descoberta, avaliação ou migração em uma geografia diferente, você pode precisar de vários projetos.
- **Planos de aparelhos**: O Azure Migrate usa um aparelho Azure Migrate no local, implantado em uma máquina Windows, para descobrir continuamente servidores para avaliação e migração. O aparelho monitora as mudanças de ambiente, como a adição de VMs, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos aparelhos usar.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para o planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos do Azure Migrate** | Avalie até 35.000 servidores em um projeto.
**Dispositivo de Migrações para Azure** | Um aparelho pode descobrir até 250 servidores.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 servidores em um único grupo.
**Avaliação do Azure Migrate** | Você pode avaliar até 35.000 servidores em uma única avaliação.


## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do aparelho, você tem que selecionar cada servidor físico. 

## <a name="prepare-for-assessment"></a>Prepare-se para avaliação

Prepare o Azure e servidores físicos para avaliação do servidor. 

1. Verifique [os requisitos e limitações do suporte físico ao servidor.](migrate-support-matrix-physical.md)
2. Configure permissões para sua conta do Azure interagir com o Azure Migrate.
3. Preparem os servidores físicos.

Siga as instruções [neste tutorial](tutorial-prepare-physical.md) para configurar essas configurações.

## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar um projeto do Migrações para Azure.
2. Adicione a ferramenta Azure Migrate Server Assessment aos projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e revisar uma avaliação

1. Crie avaliações para servidores físicos.
1. Revisar as avaliações em preparação para o planejamento migratório.

[Saiba mais](tutorial-assess-physical.md) sobre como criar e revisar avaliações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar avaliações do Azure Migrate para servidores físicos.
> * Preparado Azure e servidores físicos para avaliação.
> * Criou um projeto do Azure Migrate e fez avaliações.
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como modificar [avaliações.](how-to-modify-assessment.md)
