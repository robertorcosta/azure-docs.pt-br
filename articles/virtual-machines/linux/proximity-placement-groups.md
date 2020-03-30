---
title: Use grupos de colocação de proximidade para VMs Linux
description: Aprenda a criar e usar grupos de colocação de proximidade para máquinas virtuais Linux no Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171042"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Implantar VMs para grupos de colocação de proximidade usando o Azure CLI

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um [grupo de colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados perto um do outro. Grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Crie o grupo de colocação de proximidade
Crie um grupo de [`az ppg create`](/cli/azure/ppg#az-ppg-create)colocação de proximidade usando . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Grupos de colocação de proximidade de lista

Você pode listar todos os seus grupos de colocação de proximidade usando [a lista az ppg](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM dentro do grupo de colocação de proximidade usando [novo az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Você pode ver o VM no grupo de colocação de proximidade usando [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de colocação de proximidade. Use o `--ppg` mesmo parâmetro com [a configuração de disponibilidade az vm para](/cli/azure/vm/availability-set#az-vm-availability-set-create) criar um conjunto de disponibilidade e todas as VMs no conjunto de disponibilidade também serão criadas no mesmo grupo de colocação de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de escalas em seu grupo de colocação de proximidade. Use o `--ppg` mesmo parâmetro com [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) para criar um conjunto de escalas e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os comandos [Azure CLI](/cli/azure/ppg) para grupos de colocação de proximidade.
