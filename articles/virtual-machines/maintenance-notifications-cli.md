---
title: Obter notificações de manutenção usando a CLI
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento, usando o CLI do Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 289733c4cee23a37c26df0b613a470925756f0eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84674832"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Manipulando notificações de manutenção planejada usando o CLI do Azure

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Você pode usar a CLI para ver quando as VMs estão agendadas para [manutenção](maintenance-notifications.md). As informações de manutenção planejada estão disponíveis em [AZ VM Get-Instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
As informações de manutenção só serão retornadas se houver manutenção planejada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Iniciar manutenção

A chamada a seguir iniciará a manutenção em uma VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

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

Você também pode manipular a manutenção planejada usando o [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md).
