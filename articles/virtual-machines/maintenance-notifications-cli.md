---
title: Receba notificações de manutenção usando a CLI
description: Veja notificações de manutenção de máquinas virtuais em execução no Azure e inicie a manutenção de autoatendimento, usando o Cli do Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920885"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Manuseio de notificações planejadas de manutenção usando o Azure CLI

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Você pode usar a CLI para ver quando as VMs estão programadas para [manutenção](maintenance-notifications.md). As informações de manutenção planejadas estão disponíveis a partir [da exibição az vm get-instance](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
As informações de manutenção só serão retornadas se houver manutenção planejada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Iniciar manutenção

A chamada a seguir iniciará `IsCustomerInitiatedMaintenanceAllowed` a manutenção em uma VM se estiver definida como verdadeira.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Implantações clássicas

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Caso você ainda tenha VMs herdadas que foram implantadas usando o modelo de implantação clássico, use a CLI Clássica do Azure para consultar VMs e iniciar a manutenção.

Verifique se você está no modo correto para trabalhar com a VM clássica digitando:

```
azure config mode asm
```

Para obter o status de manutenção de uma VM chamada *myVM*, digite:

```
azure vm show myVM 
``` 

Para iniciar a manutenção na VM clássica chamada *myVM* no serviço *myService* e na implantação *myDeployment*, digite:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Próximas etapas

Você também pode lidar com manutenção planejada usando o [Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).
