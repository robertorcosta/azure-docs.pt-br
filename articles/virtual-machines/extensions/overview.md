---
title: Extensões e recursos da máquina virtual do Azure
description: Saiba o que são as extensões Azure VM e como usá-las com as máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072969"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensões e recursos da máquina virtual do Azure
Extensões de máquina virtual do Azure (VM) são pequenos aplicativos que fornecem as tarefas de configuração e automação de pós-implantação em VMs do Azure, você pode usar imagens existentes e personalizá-las como parte de suas implantações, você saindo da empresa de criação de imagem personalizada.

A plataforma do Azure hospeda várias extensões que variam de configuração de VM, monitoramento, segurança e aplicativos de utilidade. Os publicadores tomam um aplicativo, e o envolvem em uma extensão e simplificam a instalação, assim tudo que você precisa fazer é fornecer parâmetros obrigatórios. 

 Há uma opção grande de primeiras e extensões de terceiros, se o aplicativo no repositório de extensão não existir, você pode usar a extensão de Script personalizado e configurar sua VM com seus próprios comandos e scripts.

Exemplos de cenários-chave cujas extensões são usadas para:
* Configuração da VM, você pode usar a DSC (Configuração de Estado Desejado) do Powershell, Extensões Chef, Puppet e Custom Script para instalar os agentes de configuração da VM e configurar a sua VM. 
* Produtos AV, como Symantec, ESET.
* Ferramenta de vulnerabilidade VM, como Qualys, Rapid7, HPE.
* Ferramentas de VM e aplicativo, como DynaTrace, Observador de Rede do Azure, Site24x7, e Stackify.

As extensões podem ser incluídas com uma nova implantação de VM. Por exemplo, podem fazer parte de uma implantação maior, configuração de aplicativos no provisionamento de VM, ou executar em qualquer pós-implantação de sistemas operado por  extensão com suporte.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como localizar quais extensões estão disponíveis?
Você pode exibir as extensões disponíveis na folha da VM no Portal, em extensões, isso representa apenas uma pequena quantidade para a lista completa, você pode usar as ferramentas CLI, consulte [Descobrindo as Extensões VM para Linux](features-linux.md) e [ Descobrindo as Extensões VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como instalar uma extensão?
As extensões de VM do Azure podem ser gerenciadas usando a CLI do Azure, o Azure PowerShell, os modelos do Azure Resource Manager e o portal do Azure. Para testar uma extensão, você pode ir para o portal do Azure, selecionar a extensão de Script personalizado, em seguida, passar um comando / script e executar as extensões.

Caso deseje a mesma extensão que você adicionou no portal pela CLI ou pelo modelo do Resource Manager, confira a documentação de uma extensão diferente, como a [Extensão de Script Personalizado do Windows](custom-script-windows.md) e a [Extensão de Script Personalizado do Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como gerenciar o ciclo de vida do aplicativo de extensão?
Você não precisa se conectar a uma VM diretamente para instalar ou excluir a extensão. Como o ciclo de vida do aplicativo de extensão do Azure é gerenciado fora da VM e integrado à plataforma do Azure, também é possível obter status integrado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Qualquer outra coisa que devo estar pensando sobre extensões?
As extensões instalam aplicativos, como os aplicativos, há alguns requisitos, para extensões existe uma lista de Windows e Linux OSes compatíveis e você  precisa ter os agentes de VM do Azure instalados. Alguns aplicativos de extensão VM individuais podem ter seus próprios pré-requisitos de ambiente, como acesso a um ponto de extremidade.

## <a name="troubleshoot-extensions"></a>Solucionar problemas de extensões

As informações de solução de problemas para cada extensão podem ser encontradas na **seção Solução de problemas e** na seção de suporte na visão geral da extensão. Aqui está uma lista das informações de solução de problemas disponíveis:

| Namespace | Solução de problemas |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Dependência do Monitor do Azure para Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Dependência do Monitor do Azure para Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Criptografia de disco azure para Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure Disk Encryption para o Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Script personalizado para Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Configuração de estado desejada para Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Configuração de estado desejada para Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Extensão do driver de GPU NVIDIA para Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Extensão de Driver NVIDIA GPU para Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Extensão antimalware para Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Monitor Azure para Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Monitor Azure para Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace para Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Redefinir senha (VMAccess) para Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como as extensões e agente do Linux funcionam, consulte [extensões de VM do Azure e recursos para Linux](features-linux.md).
* Para obter mais informações sobre como as extensões e agente do Windows funcionam, consulte [extensões de VM do Azure e recursos para Windows](features-windows.md).  
* Para instalar o Agente Convidado do Windows, consulte Visão geral do [agente de máquina virtual do Windows Azure](agent-windows.md).  
* Para instalar o Agente Linux, consulte Visão geral do [Agente de Máquina Virtual do Azure Linux](agent-linux.md).  

