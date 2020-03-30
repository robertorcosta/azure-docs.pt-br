---
title: Avalie um grande número de VMs hiper-v para migração para o Azure com o Azure Migrate | Microsoft Docs
description: Descreve como avaliar um grande número de VMs Hiper-V para migração para o Azure usando o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279447"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Avalie um grande número de VMs hiper-v para migração para o Azure

Este artigo descreve como avaliar um grande número de VMs hiper-V no local para migração para o Azure, usando a ferramenta Azure Migrate Server Assessment.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 


Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure as permissões do Azure e prepare o Hyper-V para avaliação.
> * Crie um projeto do Azure Migrate e crie uma avaliação.
> * Revise a avaliação como você planeja para a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar um par de VMs antes de avaliar em escala, siga nossa [série tutorial](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planejar a avaliação de um grande número de VMs Hiper-V, há algumas coisas para pensar:

- **Planos Projetos do Azure Migrate**: Descubra como implantar projetos do Azure Migrate. Por exemplo, se seus data centers estão em diferentes geografias ou você precisa armazenar metadados relacionados à descoberta, avaliação ou migração em uma geografia diferente, você pode precisar de vários projetos.
- **Planos de aparelhos**: O Azure Migrate usa um aparelho Azure Migrate no local, implantado como um Hyper-V VM, para descobrir continuamente VMs para avaliação e migração. O aparelho monitora as mudanças de ambiente, como a adição de VMs, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos aparelhos usar.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para o planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos do Azure Migrate** | Avalie até 35.000 VMs em um projeto.
**Dispositivo de Migrações para Azure** | Um aparelho pode descobrir até 5000 VMs.<br/> Um aparelho pode se conectar a até 300 hosts Hyper-V.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/> Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 VMs em um único grupo.
**Avaliação do Azure Migrate** | Você pode avaliar até 35.000 VMs em uma única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do aparelho, você tem que selecionar cada host Hyper-V. 
- Se você está executando um ambiente de vários inquilinos, você não pode atualmente descobrir apenas VMs que pertencem a um inquilino específico. 

## <a name="prepare-for-assessment"></a>Prepare-se para avaliação

Prepare o Azure e o Hyper-V para avaliação do servidor. 

1. Verifique [os requisitos e limitações do suporte ao Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configure permissões para sua conta do Azure interagir com o Azure Migrate
3. Prepare hosts e VMs hyper-v

Siga as instruções [neste tutorial](tutorial-prepare-hyper-v.md) para configurar essas configurações.

## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Crie um projeto de Migração do Azure.
2. Adicione a ferramenta Azure Migrate Server Assessment aos projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e revisar uma avaliação

1. Crie avaliações para VMs Hiper-V.
1. Revisar as avaliações em preparação para o planejamento migratório.

[Saiba mais](tutorial-assess-hyper-v.md) sobre como criar e revisar avaliações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para escalar avaliações do Azure Migrate para Hiper-VMs
> * Preparado Azure e Hyper-V para avaliação
> * Criou um projeto do Azure Migrate e fiz avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como modificar [avaliações](how-to-modify-assessment.md)
