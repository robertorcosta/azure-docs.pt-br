---
title: Habilitar e desativar o Console Serial Azure | Microsoft Docs
description: Como ativar e desativar o serviço de console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451295"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Habilite e desative o Console Serial Do Azure

Assim como qualquer outro recurso, o Azure Serial Console pode ser ativado e desativado. Serial Console é habilitado por padrão para todas as assinaturas no Azure global. Atualmente, desativar o Console Serial desativará o serviço para toda a sua assinatura. Desativar ou reativar o Serial Console para uma assinatura requer acesso ao nível do contribuinte ou acima na assinatura.

Você também pode desativar o console serial para uma instância de configuração de escala de VM ou virtual individual, desativando diagnósticos de inicialização. Você exigirá acesso ao nível do contribuinte ou acima no conjunto de escala vm/máquina virtual e na conta de armazenamento de diagnósticos de inicialização.

## <a name="vm-level-disable"></a>Desabilitação no nível da VM
O console serial pode ser desativado para uma escala específica de VM ou máquina virtual definida desativando a configuração de diagnóstico de inicialização. Desative os diagnósticos de inicialização do portal Azure para desativar o console serial para o conjunto de escalas da VM ou da máquina virtual. Se você estiver usando o console serial em um conjunto de escala de máquina virtual, certifique-se de atualizar as instâncias de conjunto de escala da máquina virtual para o modelo mais recente.


## <a name="subscription-level-enabledisable"></a>Habilitação/desativar em nível de assinatura

> [!NOTE]
> Certifique-se de que você está na nuvem certa (Azure Public Cloud, Azure US Government Cloud) antes de executar este comando. Você pode `az cloud list` verificar e definir `az cloud set -n <Name of cloud>`sua nuvem com .

### <a name="azure-cli"></a>CLI do Azure

O console serial pode ser desativado e reativado para uma assinatura inteira usando os seguintes comandos no Cli do Azure (você pode usar o botão "Experimentá-lo" para iniciar uma instância do Azure Cloud Shell no qual você pode executar os comandos):

Para desativar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para obter o status atual ativado/desativado do console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

O console serial também pode ser ativado e desativado usando o PowerShell.

Para desativar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Console Serial Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [Console Serial Azure para VMs Windows](./serial-console-windows.md)
* Conheça as [opções de gerenciamento de energia dentro do Console Serial Do Azure](./serial-console-power-options.md)