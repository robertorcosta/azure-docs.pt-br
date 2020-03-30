---
title: Avalie um grande número de VMs vmware para migração para o Azure com o Azure Migrate
description: Descreve como avaliar um grande número de VMs VMware para migração para o Azure usando o serviço Azure Migrate.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336864"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Avalie um grande número de VMs vmware para migração para o Azure


Este artigo descreve como avaliar grandes números (1000-35.000) de VMMs VMware locais para migração para o Azure, usando a ferramenta Azure Migrate Server Assessment.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 

Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configure as permissões do Azure e prepare o VMware para avaliação.
> * Crie um projeto do Azure Migrate e crie uma avaliação.
> * Revise a avaliação como você planeja para a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar um par de VMs antes de avaliar em escala, siga nossa [série tutorial](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plano de avaliação

Ao planejar a avaliação de um grande número de VMware VMs, há algumas coisas para pensar:

- **Planos Projetos do Azure Migrate**: Descubra como implantar projetos do Azure Migrate. Por exemplo, se seus data centers estão em diferentes geografias ou você precisa armazenar metadados relacionados à descoberta, avaliação ou migração em uma geografia diferente, você pode precisar de vários projetos. 
- **Planos de aparelhos**: O Azure Migrate usa um aparelho Azure Migrate no local, implantado como VMware VM, para descobrir continuamente VMs. O aparelho monitora as mudanças de ambiente, como a adição de VMs, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos aparelhos você precisa implantar.
- **Plano conta para descoberta**: O aparelho Azure Migrate usa uma conta com acesso ao vCenter Server para descobrir VMs para avaliação e migração. Se você estiver descobrindo mais de 10.000 VMs, configure várias contas.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para o planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos do Azure Migrate** | Avalie até 35.000 VMs em um projeto.
**Dispositivo de Migrações para Azure** | Um aparelho pode descobrir até 10.000 VMs em um vCenter Server.<br/> Um aparelho só pode se conectar a um único servidor vCenter.<br/> Um aparelho só pode ser associado a um único projeto Azure Migrate.<br/>  Qualquer número de aparelhos pode ser associado a um único projeto Azure Migrate. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 VMs em um único grupo.
**Avaliação do Azure Migrate** | Você pode avaliar até 35.000 VMs em uma única avaliação.

Com esses limites em mente, aqui estão alguns exemplos de implantações:


**vCenter Server** | **VMs no servidor** | **Recomendação** | **Ação**
---|---|---
Um | < 10.000 | Um projeto azure migrate.<br/> Um aparelho.<br/> Uma conta vCenter para descoberta. | Configure o aparelho, conecte-se ao vCenter Server com uma conta.
Um | > 10.000 | Um projeto azure migrate.<br/> Vários aparelhos.<br/> Várias contas vCenter. | Configure o aparelho para cada 10.000 VMs.<br/><br/> Configure contas vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 VMs.<br/> Conecte cada aparelho ao servidor vCenter com uma conta.<br/> Você pode analisar dependências entre máquinas que são descobertas com diferentes aparelhos.
Vários | < 10.000 |  Um projeto azure migrate.<br/> Vários aparelhos.<br/> Uma conta vCenter para descoberta. | Configure os aparelhos, conecte-se ao vCenter Server com uma conta.<br/> Você pode analisar dependências entre máquinas que são descobertas com diferentes aparelhos.
Vários | > 10.000 | Um projeto azure migrate.<br/> Vários aparelhos.<br/> Várias contas vCenter. | Se a descoberta do vCenter Server < 10.000 VMs, configure um aparelho para cada servidor vCenter.<br/><br/> Se a descoberta do vCenter Server > 10.000 VMs, configure um aparelho para cada 10.000 VMs.<br/> Configure contas vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 VMs.<br/> Conecte cada aparelho ao servidor vCenter com uma conta.<br/> Você pode analisar dependências entre máquinas que são descobertas com diferentes aparelhos.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Descoberta de planos em um ambiente multi-inquilino

Se você está planejando um ambiente de vários inquilinos, você pode escopo da descoberta no vCenter Server.

- Você pode definir o escopo de detecção do aparelho em um data centers do vCenter Server, clusters ou pasta de clusters, hosts ou pasta de hosts ou VMs individuais.
- Se o seu ambiente for compartilhado entre os inquilinos e você quiser descobrir cada inquilino separadamente, você pode escopo de acesso à conta vCenter que o aparelho usa para ser descoberta. 
    - Você pode querer escopo por pastas VM se os inquilinos compartilharem hosts. O Azure Migrate não pode descobrir VMs se a conta vCenter tiver acesso concedido no nível da pasta vCenter VM. Se você pretende definir o escopo da descoberta por pastas de VM, faça isso verificando se a conta do vCenter tem acesso somente leitura atribuído em um nível de VM. [Saiba mais](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Prepare-se para avaliação

Prepare o Azure e o VMware para avaliação do servidor. 

1. Verifique [os requisitos e limitações do suporte ao VMware](migrate-support-matrix-vmware.md).
2. Configure permissões para sua conta do Azure interagir com o Azure Migrate.
3. Prepare a VMware para avaliação.

Siga as instruções [neste tutorial](tutorial-prepare-vmware.md) para configurar essas configurações.


## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Crie um projeto de Migração do Azure.
2. Adicione a ferramenta Azure Migrate Server Assessment aos projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e revisar uma avaliação

1. Crie avaliações para VMware VMs.
1. Revisar as avaliações em preparação para o planejamento migratório.


Siga as instruções [neste tutorial](tutorial-assess-vmware.md) para configurar essas configurações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para escalar avaliações do Azure Migrate para VMs VMware
> * Preparado Azure e VMware para avaliação
> * Criou um projeto do Azure Migrate e fiz avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como as](concepts-assessment-calculation.md) avaliações são calculadas e como modificar [avaliações.](how-to-modify-assessment.md)
