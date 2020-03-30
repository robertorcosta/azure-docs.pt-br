---
title: Use cli para implantar VMs do Azure Spot (Preview)
description: Aprenda a usar a CLI para implantar VMs Azure Spot para economizar custos.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163085"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Visualização: Implantar VMs spot usando o Cli Azure

O uso [de VMs do Azure Spot](spot-vms.md) permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Você tem opção de definir um preço máximo que você está disposto a pagar, por hora, para a VM. O preço máximo de um VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis. Para obter mais informações sobre como definir o preço máximo, consulte [Spot VMs - Pricing](spot-vms.md#pricing).

O processo para criar uma VM com spot usando o Azure CLI é o mesmo que detalhado no [artigo quickstart](/azure/virtual-machines/linux/quick-create-cli). Basta adicionar o parâmetro '-priority Spot' e `-1`fornecer um preço máximo ou .

> [!IMPORTANT]
> As instâncias spot estão atualmente em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="install-azure-cli"></a>Instalar a CLI do Azure.

Para criar VMs spot, você precisa estar executando a versão 2.0.74 do Azure CLI ou posterior. Execute **az --version** para descobrir a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Entrar no Azure usando [login az](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Criar um VM spot

Este exemplo mostra como implantar uma VM Linux Spot que não será despejada com base no preço. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Depois que a VM for criada, você pode consultar para ver o preço máximo de faturamento de todas as VMs no grupo de recursos.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Próximas etapas**

Você também pode criar uma VM spot usando [o Azure PowerShell](../windows/spot-powershell.md) ou um [modelo](spot-template.md).

Se você encontrar um erro, consulte [Códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
