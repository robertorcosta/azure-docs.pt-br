---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76749003"
---
Este artigo fornece uma visão geral dos recursos de disponibilidade das máquinas virtuais Azure (VMs).

## <a name="high-availability"></a>Alta disponibilidade

As cargas de trabalho são normalmente espalhadas por diferentes máquinas virtuais para obter alto rendimento, desempenho e para criar redundância no caso de uma VM ser impactada devido a uma atualização ou outro evento. 

Existem poucas opções que o Azure oferece para alcançar alta disponibilidade. Primeiro vamos falar sobre construções básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidade

[As zonas](../articles/availability-zones/az-overview.md) de disponibilidade expandem o nível de controle que você precisa para manter a disponibilidade dos aplicativos e dados em suas VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada, dentro de uma região azure. Há três Zonas de Disponibilidade por região do Azure com suporte. 

Cada zona de disponibilidade tem uma rede, resfriamento e fonte de energia distintos. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e seus dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou do [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma Zona de Disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. 

### <a name="update-domains"></a>Atualizar domínios

Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. 

Essa abordagem garante que pelo menos uma instância do aplicativo sempre permaneça em execução enquanto a plataforma Windows Azure passar por manutenção periódica. A ordem de domínios de atualização que estão sendo reinicializados pode não prosseguir sequencialmente durante a manutenção, mas apenas um domínio de atualização é reiniciado por vez.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de Escala de Máquinas Virtuais 

Os conjuntos de escala de máquinas virtuais do Azure permitem criar e gerenciar um grupo de VMs balanceadas de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Os conjuntos de escala fornecem alta disponibilidade para seus aplicativos e permitem que você gerencie, configure e atualize centralmente muitas VMs. Recomendamos que duas ou mais VMs sejam criadas dentro de uma escala definida para fornecer um aplicativo altamente disponível e para atender ao [SLA Azure de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há custo para o conjunto de escala em si, você só paga por cada instância de VM que você cria. Quando uma única VM está usando [SSDs premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada. Máquinas virtuais em um conjunto de escalas podem ser implantadas em vários domínios de atualização e domínios de falhas para maximizar a disponibilidade e a resiliência a paralisações devido a paralisações no data center e eventos de manutenção planejados ou não. As máquinas virtuais em um conjunto de escalas também podem ser implantadas em uma única zona de disponibilidade, ou regionalmente. As opções de implantação da zona de disponibilidade podem diferir com base no modo de orquestração.

### <a name="preview-orchestration-mode-preview"></a>Pré-visualização: Visualização do modo de orquestração
Os conjuntos de escala de máquinas virtuais permitem especificar o modo de orquestração.  Com o modo de orquestração (visualização) de configuração de escala de máquina virtual, agora você pode escolher se o conjunto de escalas deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de escala, ou instâncias de máquinas virtuais criadas implicitamente com base no modelo de configuração. Escolha o modo de orquestração que o modelo de orquestração VM permite agrupar máquinas virtuais explicitamente definidas em uma região ou em uma zona de disponibilidade. As máquinas virtuais implantadas em uma Zona de Disponibilidade fornecem isolamento zonal às VMs se elas estão vinculadas ao limite da zona de disponibilidade e não estão sujeitas a quaisquer falhas que possam ocorrer em outra zona de disponibilidade na região. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Modelo de configuração vm| Nenhum. VirtualMachineProfile é indefinido no modelo de conjunto de escalas. | Obrigatórios. VirtualMachineProfile é preenchido no modelo de conjunto de escalas. |
| Adicionando nova VM ao conjunto de escalas| As VMs são explicitamente adicionadas ao conjunto de escalas quando a VM é criada. | As VMs são implicitamente criadas e adicionadas ao conjunto de escalas com base no modelo de configuração vm, contagem de instâncias e regras de AutoScaling. |
| Zonas de Disponibilidades| Suporta implantação regional ou VMs em uma Zona de Disponibilidade| Suporta implantação regional ou várias Zonas de Disponibilidade; Pode definir a estratégia de balanceamento de zona |
| Domínios de falha| Pode definir a contagem de domínios de falha. 2 ou 3 com base no suporte regional e 5 para zona de disponibilidade. O domínio de falha vm atribuído persistirá com o ciclo de vida da VM, incluindo deloloe e reinicialização. | Pode definir 1, 2 ou 3 domínios de falha para implantações não zonais e 5 para implantações de zona de disponibilidade. O domínio de falha vm atribuído não persiste com o ciclo de vida da VM, as máquinas virtuais são atribuídas a um domínio de falha no momento da alocação. |
| Atualizar domínios| N/D Os domínios de atualização são automaticamente mapeados para domínios de falha| N/D Os domínios de atualização são automaticamente mapeados para domínios de falha |

**Domínios de falhas e domínios de atualização**

Os conjuntos de escala de máquinas virtuais simplificam o design para alta disponibilidade, alinhando domínios de falhas e atualizando domínios. Você só terá que definir a contagem de domínios de falha para o conjunto de escalas. O número de domínios de falha disponíveis para os conjuntos de escala pode variar de acordo com a região. Consulte [Gerenciar a disponibilidade de máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs em um datacenter que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar. Quando uma única VM está usando [SSDs premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada.

Em um conjunto de disponibilidade, as VMs são distribuídas automaticamente entre esses domínios de falha. Essa abordagem limita o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

Para VMs que usam [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), as VMs são alinhadas aos domínios de falha de disco gerenciado quando usam um conjunto de disponibilidade gerenciada. Esse alinhamento garante que todos os discos gerenciados anexados a uma VM fiquem no mesmo domínio de falha de disco gerenciado. 

Somente as VMs com discos gerenciados podem ser criadas em um conjunto de disponibilidade gerenciado. O número de domínios de falha de disco gerenciado varia por região - dois ou três domínios de falha de disco gerenciados por região. Você pode ler mais sobre esses gerenciados domínios de falha de disco para [VMs do Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


As VMs dentro de um conjunto de disponibilidade também são distribuídas automaticamente entre domínios de atualização. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

