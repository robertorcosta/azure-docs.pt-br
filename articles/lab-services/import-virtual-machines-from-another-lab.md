---
title: Importe máquinas virtuais de outro laboratório no Azure DevTest Labs
description: Este artigo descreve como importar máquinas virtuais de outro laboratório para o laboratório atual no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759509"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importe máquinas virtuais de outro laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como importar máquinas virtuais de outro laboratório para o seu laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão alguns cenários onde você precisa importar VMs de um laboratório para outro laboratório:

- Um indivíduo na equipe está se movendo para outro grupo dentro da empresa e quer levar o desktop do desenvolvedor para o DevTest Labs da nova equipe.
- O grupo atingiu uma [cota de nível de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md) e quer dividir as equipes em algumas assinaturas
- A empresa está se mudando para o Express Route (ou alguma outra nova topologia de rede) e a equipe quer mover as Máquinas Virtuais para usar essa nova infra-estrutura

## <a name="solution-and-constraints"></a>Solução e restrições
Esse recurso permite importar VMs em um laboratório (fonte) para outro laboratório (destino). Você pode opcionalmente dar um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências, como discos, horários, configurações de rede e assim por diante.

O processo leva algum tempo e é impactado pelos seguintes fatores:

- Número/tamanho dos discos conectados à máquina de origem (já que é uma operação de cópia e não uma operação de movimento)
- Distância até o destino (por exemplo, região leste dos EUA para sudeste da Ásia).

Uma vez que o processo esteja concluído, a máquina virtual de origem permanece desligada e a nova está sendo executado no laboratório de destino.

Existem duas restrições fundamentais a serem cientes quando planejam importar VMs de um laboratório para outro laboratório:

- As importações da Máquina Virtual entre assinaturas e em todas as regiões são suportadas, mas as assinaturas devem ser associadas ao mesmo inquilino do Azure Active Directory.
- As máquinas virtuais não devem estar em um estado de reivindicação no laboratório de origem.
- Você é o dono da VM no laboratório de origem e dono do laboratório no laboratório de destino.
- Atualmente, esse recurso é suportado apenas através do Powershell e da API REST.

## <a name="use-powershell"></a>Usar o PowerShell
Baixe o arquivo ImportVirtualMachines.ps1 do [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Você pode usar o script para importar uma única VM ou todas as VMs no laboratório de origem para o laboratório de destino.

### <a name="use-powershell-to-import-a-single-vm"></a>Use o PowerShell para importar um único VM
A execução deste script powershell requer identificar a VM de origem e o laboratório de destino e, opcionalmente, fornecer um novo nome para usar na máquina de destino:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Use o PowerShell para importar todas as VMs no laboratório de origem
Se a Máquina Virtual Fonte não for especificada, o script importa automaticamente todas as VMs nos DevTest Labs.  Por exemplo: 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Use HTTP REST para importar uma VM
A chamada REST é simples. Você fornece informações suficientes para identificar os recursos de origem e destino. Lembre-se que a operação ocorre no recurso do laboratório de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
