---
title: Use ferramentas de linha de comando para iniciar e parar vMs Azure DevTest Labs
description: Aprenda a usar ferramentas de linha de comando para iniciar e parar máquinas virtuais no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169243"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Use ferramentas de linha de comando para iniciar e parar as máquinas virtuais Azure DevTest Labs
Este artigo mostra como usar o Azure PowerShell ou o Azure CLI para iniciar ou parar máquinas virtuais em um laboratório no Azure DevTest Labs. Você pode criar scripts PowerShell/CLI para automatizar essas operações. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
O Azure DevTest Labs é uma maneira de criar ambientes de dev/teste rápidos, fáceis e enxutos. Ele permite gerenciar custos, provisionar rapidamente VMs e minimizar desperdícios.  Existem recursos incorporados no portal Azure que permitem configurar VMs em um laboratório para iniciar e parar automaticamente em momentos específicos. 

No entanto, em alguns cenários, você pode querer automatizar a partida e a parada de VMs a partir de scripts PowerShell/CLI. Ele lhe dá alguma flexibilidade para iniciar e parar máquinas individuais a qualquer momento em vez de em momentos específicos. Aqui estão algumas das situações em que executar essas tarefas usando scripts seria útil.

- Ao usar um aplicativo de 3 níveis como parte de um ambiente de teste, os níveis precisam ser iniciados em uma seqüência. 
- Desligue uma VM quando um critério personalizado for cumprido para economizar dinheiro. 
- Use-o como uma tarefa dentro de um fluxo de trabalho ci/cd para iniciar no início do fluxo, usar as VMs como máquinas de construção, máquinas de teste ou infra-estrutura e, em seguida, parar as VMs quando o processo estiver concluído. Um exemplo disso seria a fábrica de imagens personalizadas com o Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> O script a seguir usa o módulo Azure PowerShell Az. 

O script PowerShell a seguir inicia um VM em um laboratório. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) é o foco principal deste script. O **parâmetro ResourceId** é o id de recurso totalmente qualificado para o VM no laboratório. O parâmetro **Ação** é onde as opções **Iniciar** ou **Parar** são definidas dependendo do que for necessário.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>CLI do Azure
O [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) é outra maneira de automatizar a partida e a parada das VMs do DevTest Labs. O Azure CLI pode ser [instalado](/cli/azure/install-azure-cli?view=azure-cli-latest) em diferentes sistemas operacionais. O script a seguir oferece comandos para iniciar e parar uma VM em um laboratório. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir para usar o portal Azure para fazer essas operações: [Reiniciar uma VM](devtest-lab-restart-vm.md).
