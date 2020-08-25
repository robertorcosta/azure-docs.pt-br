---
title: Usar o portal para implantar VMs de ponto do Azure
description: Como usar Azure PowerShell para implantar VMs pontuais para economizar nos custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: ef5fa0aafca1312480f51614a1ba1692c09a13b8
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816568"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Implantar VMs especiais usando o portal do Azure

O uso de [VMs pontuais](../spot-vms.md) permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [VMs pontuais – preços](../spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.05701` seria um preço máximo de $0.5701 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.

Quando a VM é removida, você tem a opção de excluir a VM e o disco subjacente ou desalocar a VM para que ela possa ser reiniciada mais tarde.


## <a name="create-the-vm"></a>Criar a VM

O processo para criar uma VM que usa VMs pontuais é o mesmo detalhado no guia de [início rápido](quick-create-portal.md). Ao implantar uma VM, você pode optar por usar uma instância de spot do Azure.


Na guia **noções básicas** , na seção **detalhes da instância** , **não** é o padrão para usar uma instância de spot do Azure.

![Captura de tela para escolher não, não use uma instância de spot do Azure](media/spot-portal/no.png)

Você seleciona **Sim**, a seção é expandida e você pode escolher o [tipo de remoção e a política de remoção](../spot-vms.md#eviction-policy). 

![Captura de tela para escolher Sim, usar uma instância de spot do Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Próximas etapas

Você também pode criar VMs pontuais usando o [PowerShell](spot-powershell.md).