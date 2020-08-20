---
title: Conjuntos de dimensionamento de máquinas virtuais e Standard Load Balancer do Azure
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 41f1d6c18eab35bd1a41d4cfa98d0cbda69b35ac
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650325"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer com conjuntos de dimensionamento de máquinas virtuais do Azure

Ao trabalhar com conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, as seguintes diretrizes devem ser consideradas:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Regras NAT de encaminhamento de porta e de entrada:
  * Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais do Azure, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.
  * Ao usar o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, as regras de NAT de entrada padrão são criadas automaticamente.
## <a name="inbound-nat-pool"></a>Pool de NAT de entrada:
  * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos um pool de NAT de entrada. 
  * O pool de NAT de entrada é uma coleção de regras de NAT de entrada. Um pool de NAT de entrada não dá suporte a vários conjuntos de escalas de máquina virtual.
  * Para excluir um pool de NAT de um conjunto de dimensionamento de máquinas virtuais existente, primeiro você precisa remover o pool NAT do conjunto de dimensionamento. Um exemplo completo usando A CLI é mostrado abaixo:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Regras de balanceamento de carga:
  * Ao usar o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, a regra de balanceamento de carga padrão é criada automaticamente.
## <a name="outbound-rules"></a>Regras de saída:
  *  Para criar uma regra de saída para um pool de back-end que já é referenciado por uma regra de balanceamento de carga, você precisa primeiro marcar **"criar regras de saída implícitas"** como **não** no portal quando a regra de balanceamento de carga de entrada é criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Criação de regra de balanceamento de carga" border="true":::

Os métodos a seguir podem ser usados para implantar um conjunto de dimensionamento de máquinas virtuais com um balanceador de carga do Azure existente.

* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o portal do Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
