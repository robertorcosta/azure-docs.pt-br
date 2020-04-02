---
title: Use o portal para implantar VMs azure Spot
description: Aprenda a usar o Azure PowerShell para implantar VMs Spot para economizar custos.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547376"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Implantar VMs spot usando o portal Azure

O uso [de VMs Spot](spot-vms.md) permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [Spot VMs - Pricing](spot-vms.md#pricing).

Você tem opção de definir um preço máximo que você está disposto a pagar, por hora, para a VM. O preço máximo de um VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.05701`o valor seria um preço máximo de US $ 0,05701 USD por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis.


## <a name="create-the-vm"></a>Criar a VM

O processo para criar uma VM que usa VMs spot é o mesmo que detalhado no [quickstart](quick-create-portal.md). Quando você está implantando uma VM, você pode optar por usar uma instância spot do Azure.


Na guia Básico, na seção **'Exemplo de** **detalhes',** **Não** é o padrão para usar uma instância spot do Azure.

![Captura de tela para escolher não, não use uma instância de ponto do Azure](media/spot-portal/no.png)

Ele seleciona **Sim**, a seção se expande e você pode escolher seu [tipo de despejo e política de despejo](spot-vms.md#eviction-policy). 

![Captura de tela para escolher sim, use uma instância spot do Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Próximas etapas

Você também pode criar VMs spot usando [PowerShell](spot-powershell.md).