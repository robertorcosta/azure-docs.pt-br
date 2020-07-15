---
title: Mover as VMs do Azure para outra região com o Azure Site Recovery
description: Usar o Azure Site Recovery para mover as VMs do Azure de uma região do Azure para outra.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0c7efc94bcde18e7b6ff43726602fa87641f3e76
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86130626"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Mover máquinas virtuais do Azure para outra região do Azure

Este artigo fornece uma visão geral dos motivos e das etapas envolvidas na movimentação de VMs do Azure para outra região do Azure usando [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Motivos para mover VMs do Azure

Você pode mover VMs pelos seguintes motivos:

- Você já fez a implantação em uma região e foi adicionado suporte a uma nova região mais próxima aos usuários finais do aplicativo ou serviço. Nesse cenário, você pode querer mover suas VMs como está para a nova região para reduzir a latência. Use a mesma abordagem se você desejar consolidar assinaturas ou se houver regras de governança ou da organização que exijam a movimentação.
- Sua VM foi implantada como uma VM de instância única ou como parte de um conjunto de disponibilidade. Se quiser aumentar a disponibilidade dos SLAs, você poderá mover suas VMs para uma Zona de Disponibilidade.

## <a name="steps-to-move-azure-vms"></a>Etapas para mover as VMs do Azure

A movimentação das VMs envolve as seguintes etapas:

1. Verifique se os pré-requisitos.
2. Prepare as VMs de origem.
3. Prepare a região de destino.
4. Copie os dados para a região de destino. Use a tecnologia de replicação do Azure Site Recovery para copiar os dados da VM de origem para a região de destino.
5. Teste a configuração. Depois que a replicação for concluída, teste a configuração executando um failover de teste para uma rede de não produção.
6. Realize a movimentação.
7. Descarte os recursos na região de origem.

> [!NOTE]
> Detalhes sobre essas etapas são fornecidos nas seções a seguir.
> [!IMPORTANT]
> Atualmente, o Azure Site Recovery dá suporte à movimentação de VMs de uma região para outra, mas não dá suporte à movimentação dentro de uma região.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implantação de várias camadas

Esta seção descreve as arquiteturas de implantação mais comuns para um aplicativo multicamada no Azure. O exemplo é de um aplicativo de três camadas com um IP público. Cada uma das camadas (Web, aplicativo e banco de dados) tem duas VMs e é conectada por um Azure Load Balancer às outras camadas. A camada de banco de dados tem a replicação do SQL Server Always On entre as VMs para alta disponibilidade.

* **VMs de instância única implantadas em várias camadas**: Cada VM em uma camada é configurada como uma VM de instância única e é conectada por balanceadores de carga às outras camadas. Essa configuração é mais simples de adotar.

     ![Implantação de VM de instância única em camadas](media/move-vm-overview/regular-deployment.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**: Cada VM em uma camada é configurada em um conjunto de disponibilidade. Os [conjuntos de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) garantem que as VMs implantadas no Azure sejam distribuídas entre vários nós de hardware isolados em um cluster. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional.

     ![Implantação da VM em conjuntos de disponibilidade](media/move-vm-overview/avset.png)

* **VMs em cada camada implantadas em Zonas de Disponibilidade**: Cada VM em uma camada é configurada em [Zonas de Disponibilidade](../availability-zones/az-overview.md). Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

     ![Implantação de Zona de Disponibilidade](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Mover as VMs no estado em que se encontram para uma região de destino

Com base nas [arquiteturas](#typical-architectures-for-a-multi-tier-deployment) mencionadas acima, veja a seguir como será a aparência das implantações depois que você realizar a movimentação das VMs no estado em que se encontram para a região de destino.

* **VMs de instância única implantadas em várias camadas**

     ![Implantação de VM de instância única em camadas](media/move-vm-overview/single-zone.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**

     ![Conjuntos de disponibilidade entre regiões](media/move-vm-overview/crossregionaset.png)

* **VMs em cada camada implantadas em Zonas de Disponibilidade**

     ![Implantação de VMs em Zonas de Disponibilidade](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Mover as VMs para aumentar a disponibilidade

* **VMs de instância única implantadas em várias camadas**

     ![Implantação de VM de instância única em camadas](media/move-vm-overview/single-zone.png)

* **VMs em cada camada implantadas em conjuntos de disponibilidade**: Quando habilitar a replicação para sua VM usando o Azure Site Recovery, você pode configurar suas VMs em um conjunto de disponibilidade em Zonas de Disponibilidade separadas. O SLA de disponibilidade será de 99,99% depois que a operação de movimentação for concluída.

     ![Implantação de VMs em conjuntos de disponibilidade e Zonas de Disponibilidade](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> 
> * [Mover as VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
> 
> * [Mover as VMs do Azure para Zonas de Disponibilidade](move-azure-vms-avset-azone.md)

