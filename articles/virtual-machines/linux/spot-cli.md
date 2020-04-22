---
title: Use a CLI para implantar VMs azure Spot
description: Aprenda a usar a CLI para implantar VMs Azure Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 3f341271c208cc56a704c836433c33af0129a4ac
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758367"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Implantar VMs spot usando o Azure CLI

O uso [de VMs do Azure Spot](spot-vms.md) permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Você tem opção de definir um preço máximo que você está disposto a pagar, por hora, para a VM. O preço máximo de um VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis. Para obter mais informações sobre como definir o preço máximo, consulte [Spot VMs - Pricing](spot-vms.md#pricing).

O processo para criar uma VM com spot usando o Azure CLI é o mesmo que detalhado no [artigo quickstart](/azure/virtual-machines/linux/quick-create-cli). Basta adicionar o parâmetro '-priority Spot' e `-1`fornecer um preço máximo ou .


## <a name="install-azure-cli"></a>Instalar a CLI do Azure.

Para criar VMs spot, você precisa estar executando a versão 2.0.74 do Azure CLI ou posterior. Execute **az --version** para descobrir a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

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
