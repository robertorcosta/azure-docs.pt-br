---
title: Localizar e excluir NICs do Azure desconectados
description: Como localizar e excluir NICs do Azure que não estão anexadas a VMs com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 3d7a0e3405dd87d17c9d7aac2e989ab82531d9b0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036136"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Como encontrar e excluir as placas de interface de rede não anexadas (NICs) para as máquinas virtuais do Azure
Quando você exclui uma máquina virtual (VM) no Azure, as placas de interface de rede (NICs) são excluídas por padrão. Se você criar e excluir várias máquinas virtuais, os NICs não usados continuarão a usar as concessões de endereço de IP internas. Conforme você cria outras NICs de máquina virtual, podem não conseguir obter uma concessão de IP no espaço de endereço da sub-rede. Esse artigo mostra como encontrar e excluir NICs não anexadas.

## <a name="find-and-delete-unattached-nics"></a>Localizar e excluir NICs desconectados

A propriedade da *virtualMachine*para NIC armazena a ID e o grupo de recurso da máquina virtual que o NIC está anexado. Os loops de script a seguir através de todos os NICs em uma assinatura e verifica se a propriedade *virtualMachine* é nula. Se a propriedade for nula, o NIC não está anexado à máquina virtual.

Para visualizar NICs não anexados, é altamente recomendado primeiro executar o script com a variável *deleteUnattachedVHDs* como *0*. Para excluir os NICs não anexados após revisar a saída da lista, execute o script com *deleteUnattachedNics* para *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como riar e gerenciar redes virtuais no Azure, consulte [criar e gerenciar redes de máquinas virtuais](tutorial-virtual-network.md).
