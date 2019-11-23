---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414482"
---
This article provides you with an overview of the availability features of Azure virtual machines (VMs).

## <a name="high-availability"></a>Alta disponibilidade

Workloads are typically spread across different virtual machines to gain high throughput, performance, and to create redundancy in case a VM is impacted due to an update or other event. 

There are few options that Azure provides to achieve High Availability. First let’s talk about basic constructs. 

### <a name="availability-zones"></a>Zonas de disponibilidade

[Availability zones](../articles/availability-zones/az-overview.md) expand the level of control you have to maintain the availability of the applications and data on your VMs. An Availability Zone is a physically separate zone, within an Azure region. Há três Zonas de Disponibilidade por região do Azure com suporte. 

Cada zona de disponibilidade tem uma rede, resfriamento e fonte de energia distintos. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e seus dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou do [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma Zona de Disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. 

### <a name="update-domains"></a>Atualizar domínios

Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. 

Essa abordagem garante que pelo menos uma instância do aplicativo sempre permaneça em execução enquanto a plataforma Windows Azure passar por manutenção periódica. The order of update domains being rebooted may not proceed sequentially during maintenance, but only one update domain is rebooted at a time.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de Escala de Máquinas Virtuais 

Azure virtual machine scale sets let you create and manage a group of load balanced VMs. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Scale sets provide high availability to your applications, and allow you to centrally manage, configure, and update many VMs. We recommended that two or more VMs are created within a scale set to provide for a highly available application and to meet the [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). There is no cost for the scale set itself, you only pay for each VM instance that you create. Quando uma única VM está usando [SSDs premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada. Virtual machines in a scale set can be deployed across multiple update domains and fault domains to maximize availability and resilience to outages due to data center outages, and planned or unplanned maintenance events. Virtual machines in a scale set can also be deployed into a single Availability zone, or regionally. Availability zone deployment options may differ based on the orchestration mode.

### <a name="preview-orchestration-mode-preview"></a>Preview: Orchestration mode Preview
Virtual machines scale sets allow you to specify orchestration mode.  With the virtual machine scale set orchestration mode (preview), you can now choose whether the scale set should orchestrate virtual machines which are created explicitly outside of a scale set configuration model, or virtual machine instances created implicitly based on the configuration model. Choose the orchestration mode that VM orchestration model allows you group explicitly defined Virtual Machines together in a region or in an availability zone. Virtual machines deployed in an Availability Zone provides zonal isolation to VMs are they are bound to the availability zone boundary and are not subjected to any failures that may occur in other availability zone in the region. 

|   | “orchestrationMode”: “VM” (VirtualMachine)| “orchestrationMode”: “ScaleSetVM” (VirtualMachineScaleSetVM) |
|----|----|----|
| VM configuration model| Nenhuma. VirtualMachineProfile is undefined in the scale set model. | Obrigatório. VirtualMachineProfile is populated in the scale set model. |
| Adding new VM to Scale Set| VMs are explicitly added to the scale set when the VM is created. | VMs are implicitly created and added to the scale set based on the VM configuration model, instance count, and AutoScaling rules. |
| Zonas de disponibilidade| Supports regional deployment or VMs in one Availability Zone| Supports regional deployment or multiple Availability Zones; Can define the zone balancing strategy |
| Domínios de falha| Can define fault domains count. 2 or 3 based on regional support and 5 for Availability zone. The assigned VM fault domain will persist with VM lifecycle, including deallocate and restart. | Can define 1, 2, or 3 fault domains for non-zonal deployments, and 5 for Availability zone deployments. The assigned VM fault domain does not persist with VM lifecycle, virtual machines are assigned a fault domain at time of allocation. |
| Atualizar domínios| N/D Update domains are automatically mapped to fault domains| N/D Update domains are automatically mapped to fault domains |

**Fault domains and update domains**

Virtual machine scale sets simplify designing for high availability by aligning fault domains and update domains. You will only have to define fault domains count for the scale set. The number of fault domains available to the scale sets may vary by region. See [Number of Fault Domains per region](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs em um datacenter que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar. Quando uma única VM está usando [SSDs premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada.

In an availability set, VMs are automatically distributed across these fault domains. Essa abordagem limita o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

Para VMs que usam [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), as VMs são alinhadas aos domínios de falha de disco gerenciado quando usam um conjunto de disponibilidade gerenciada. Esse alinhamento garante que todos os discos gerenciados anexados a uma VM fiquem no mesmo domínio de falha de disco gerenciado. 

Somente as VMs com discos gerenciados podem ser criadas em um conjunto de disponibilidade gerenciado. O número de domínios de falha de disco gerenciado varia por região - dois ou três domínios de falha de disco gerenciados por região. Você pode ler mais sobre esses gerenciados domínios de falha de disco para [VMs do Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


VMs within an availability set are also automatically distributed across update domains. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Próximos passos
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

