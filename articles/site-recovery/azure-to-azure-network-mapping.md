---
title: Mapear redes virtuais entre duas regiões na Recuperação do Site do Azure
description: Saiba mais sobre o mapeamento de redes virtuais entre duas regiões do Azure para recuperação de desastres do Azure VM com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258077"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar mapeamento de rede e endereçamento IP para VNets

Este artigo descreve como mapear duas instâncias de VNets (redes virtuais) do Azure localizadas em diferentes regiões do Azure e como configurar o endereçamento IP entre redes. O mapeamento de rede fornece um comportamento padrão para seleção de rede de destino com base na rede de origem no momento de habilitar a replicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear as redes, você deverá ter [VNets do Azure](../virtual-network/virtual-networks-overview.md) nas regiões de origem e destino do Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurar o mapeamento da rede manualmente (Opcional)

Mapeie as redes da seguinte forma:

1. Na **Infraestrutura do Site Recovery**, clique em **+Mapeamento de Rede**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Em **Adicionar mapeamento de rede**, selecione os locais de origem e destino. Em nosso exemplo, a VM de origem está executando na região do Leste da Ásia e é replicada para a região do Sudeste Asiático.

    ![Selecionar origem e destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora crie um mapeamento de rede na direção oposta. Em nosso exemplo, a origem agora será o Sudeste Asiático e o destino será o Leste da Ásia.

    ![Adicionar painel de mapeamento de rede - Selecionar os locais de origem e destino para a rede de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapear redes ao habilitar a replicação

Se você não preparou o mapeamento de rede antes de configurar a recuperação de falhas para as VMs do Azure, poderá especificar uma rede de destino quando [configurar e habilitar a replicação](azure-to-azure-how-to-enable-replication.md). Ao fazer isso, acontecerá o seguinte:

- Baseado no destino que você selecionar, o Site Recovery criará automaticamente mapeamentos de rede da origem para a região de destino e do destino para a região de origem.
- Por padrão, o Site Recovery cria uma rede na região de destino que é idêntica à rede de origem. O Site Recovery adiciona **-asr** como um sufixo ao nome da rede de origem. É possível personalizar a rede de destino.
- Se o mapeamento de rede já ocorreu para uma rede de origem, a rede de destino mapeada será sempre o padrão no momento de habilitar replicação para mais VMs. Você pode optar por alterar a rede virtual de destino escolhendo outras opções disponíveis a partir do dropdown. 
- Para alterar a rede virtual de destino padrão para novas replicações, você precisa modificar o mapeamento de rede existente.
- Se você deseja modificar um mapeamento de rede da região A para a região B, certifique-se de excluir primeiro o mapeamento da rede da região B para a região A. Após a exclusão do mapeamento reverso, modifique o mapeamento da rede da região A para a região B e crie o mapeamento reverso relevante.

>[!NOTE]
>* Modificar o mapeamento de rede apenas altera os padrões para novas replicação de VM. Não afeta as seleções de rede virtual de destino para replicação existentes. 
>* Se você deseja modificar a rede de destino para uma replicação existente, vá para Computação e Configurações de rede do item replicado.

## <a name="specify-a-subnet"></a>Especificar uma sub-rede

A sub-rede da VM de destino é selecionada com base no nome da sub-rede da VM de origem.

- Se uma sub-rede com o mesmo nome da sub-rede da VM de origem estiver disponível na rede de destino, essa sub-rede será definida para a VM de destino.
- Se uma sub-rede com o mesmo nome não existir na rede de destino, a primeira sub-rede na ordem alfabética será definida como a sub-rede de destino.
- Você pode modificar a sub-rede de destino nas configurações **Computação e Rede** para a VM.

    ![Janela de propriedades de Computação e Rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar endereçamento IP para VMs de destino

O endereço IP de cada NIC em uma máquina virtual de destino é configurado conforme a seguir:

- **DHCP**: se o NIC da VM de origem usar DHCP, o NIC da VM de destino também será definido para usar DHCP.
- **Endereço IP estático**: se o NIC da VM de origem usar endereçamento IP estático, o NIC da VM de destino também usará um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereço IP durante failover

**Sub-redes de origem e destino** | **Detalhes**
--- | ---
Mesmo espaço de endereço | O endereço IP do NIC da VM de origem é definido como o endereço IP do NIC da VM de destino.<br/><br/> Se o endereço não estiver disponível, o próximo endereço IP disponível será definido como o destino.
Espaço de endereço diferente | O próximo endereço IP disponível na sub-rede de destino é definido como o endereço NIC da VM de destino.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereço IP durante failover de teste

**Rede de destino** | **Detalhes**
--- | ---
Rede de destino é a VNet de failover | - O endereço IP de destino será estático com o mesmo endereço IP. <br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP será o próximo disponível no final da gama de sub-redes. Por exemplo: se o endereço IP de origem for 10.0.0.19 e a rede de failover usar o intervalo 10.0.0.0/24, o próximo endereço IP atribuído à VM de destino será 10.0.0.254.
A rede de destino não é a VNet de failover | - O endereço IP de destino será estático com o mesmo endereço IP.<br/><br/>  - Se o mesmo endereço IP já estiver atribuído, o endereço IP será o próximo disponível no final da gama de sub-redes.<br/><br/> Por exemplo: se o endereço IP estático de origem for 10.0.0.19 e o failover estiver em uma rede que não seja a de failover, com o intervalo 10.0.0.0/24, o endereço IP estático de destino será 10.0.0.0.19 se disponível, e caso contrário, será 10.0.0.254.

- A rede virtual de failover é a rede de destino selecionada quando você configura a recuperação de desastre.
- É recomendável sempre usar uma rede de não produção para failover de teste.
- Você pode modificar o endereço IP de destino nas configurações **Computação e Rede** da VM.


## <a name="next-steps"></a>Próximas etapas

- Revise as [diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md) para recuperação de desastre da VM do Azure.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre como reter endereços IP após failover.
