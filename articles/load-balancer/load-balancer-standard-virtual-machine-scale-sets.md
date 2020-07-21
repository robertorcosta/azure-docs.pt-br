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
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530969"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer com conjuntos de dimensionamento de máquinas virtuais do Azure

Ao trabalhar com conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, as seguintes diretrizes devem ser consideradas:

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>Vários conjuntos de dimensionamento de máquinas virtuais não podem usar o mesmo balanceador de carga
## <a name="port-forwarding-and-inbound-nat-rules"></a>Regras NAT de encaminhamento de porta e de entrada:
  * Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais do Azure, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.
  * Ao usar o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, as regras de NAT de entrada padrão são criadas automaticamente.
## <a name="inbound-nat-pool"></a>Pool de NAT de entrada:
  * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos um pool de NAT de entrada. 
  * O pool de NAT de entrada é uma coleção de regras de NAT de entrada. Um pool de NAT de entrada não dá suporte a vários conjuntos de escalas de máquina virtual.
  
## <a name="load-balancing-rules"></a>Regras de balanceamento de carga:
  * Ao usar o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, a regra de balanceamento de carga padrão é criada automaticamente.
## <a name="outbound-rules"></a>Regras de saída:
  *  Para criar uma regra de saída para um pool de back-end que já é referenciado por uma regra de balanceamento de carga, você precisa primeiro marcar **"criar regras de saída implícitas"** como **não** no portal quando a regra de balanceamento de carga de entrada é criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Criação de regra de balanceamento de carga" border="true":::

Os métodos a seguir podem ser usados para implantar um conjunto de dimensionamento de máquinas virtuais com um balanceador de carga do Azure existente.

* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o portal do Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Configure um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
