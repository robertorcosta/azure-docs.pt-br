---
title: Usar o portal para implantar VMs de ponto do Azure
description: Saiba como usar Azure PowerShell para implantar VMs pontuais para economizar em custos.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158971"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Versão prévia: implantar VMs especiais usando o portal do Azure

O uso de [VMs pontuais](spot-vms.md) permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [VMs pontuais – preços](spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.05701`seria um preço máximo de $0.5701 USD por hora. Se você definir o preço máximo como `-1`, a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.

> [!IMPORTANT]
> Atualmente, as instâncias especiais estão em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>


## <a name="create-the-vm"></a>Criar a VM

O processo para criar uma VM que usa VMs pontuais é o mesmo detalhado no guia de [início rápido](quick-create-portal.md). Ao implantar uma VM, você pode optar por usar uma instância de spot do Azure.


Na guia **noções básicas** , na seção **detalhes da instância** , **não** é o padrão para usar uma instância de spot do Azure.

![Captura de tela para escolher não, não use uma instância de spot do Azure](media/spot-portal/no.png)

Você seleciona **Sim**, a seção é expandida e você pode escolher o [tipo de remoção e a política de remoção](spot-vms.md#eviction-policy). 

![Captura de tela para escolher Sim, usar uma instância de spot do Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Próximas etapas

Você também pode criar VMs pontuais usando o [PowerShell](spot-powershell.md).