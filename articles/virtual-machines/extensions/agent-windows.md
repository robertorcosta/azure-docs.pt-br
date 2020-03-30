---
title: Visão Geral do Agente de Máquina Virtual do Azure
description: Visão Geral do Agente de Máquina Virtual do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246117"
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do Agente de Máquina Virtual do Azure
O Agente de VM (Máquina Virtual) do Microsoft Azure é um processo seguro e leve que gerencia a interação da máquina virtual (VM) com o Controlador de Malha do Azure. O Agente de VM tem uma função fundamental na habilitação e execução de extensões de máquina virtual do Azure. Extensões de VM habilitam a configuração de VM pós-implantação, como instalação e configuração de software. Extensões de VM também habilitam os recursos de recuperação como redefinir a senha administrativa de uma VM. Sem o Agente de VM do Azure, não é possível executar extensões da VM.

Este artigo detalha a instalação e a detecção do Azure Virtual Machine Agent.

## <a name="install-the-vm-agent"></a>Instalar o Agente VM

### <a name="azure-marketplace-image"></a>Imagem do Azure Marketplace

O Agente de VM do Azure é instalado por padrão em qualquer VM Windows implantada de uma imagem do Azure Marketplace. Ao implantar uma imagem do Azure Marketplace do portal, PowerShell, Interface de Linha de Comando ou de um modelo do Azure Resource Manager, o Agente de VM do Azure também é instalado.

O Pacote de Agente de Convidado do Windows é dividido em duas partes:

- Agente de Provisionamento (PA)
- Agente de Convidado do Windows (WinGA)

Para inicializar uma VM, você deve ter o PA instalado na VM, no entanto o WinGA não precisa ser instalado. Na hora da implantação da VM, você pode optar por não instalar o WinGA. O exemplo a seguir mostra como selecionar a opção *provisionVmAgent* com um modelo do Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Se você não tiver os Agentes instalados, você não pode usar alguns dos serviços do Azure, como o Azure Backup ou segurança do Azure. Esses serviços exigem uma extensão para serem instalados. Se você tiver implantado uma VM sem o WinGA, você pode instalar a versão mais recente do agente mais tarde.

### <a name="manual-installation"></a>Instalação manual
O agente de VM do Windows pode ser instalado manualmente com um pacote do Windows Installer. Instalação manual pode ser necessária quando você cria uma imagem VM personalizada que é implantada no Azure. Para instalar manualmente o Agente de VM do Windows, [faça o download do instalador do Agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789). O VM Agent é suportado no Windows Server 2008 R2 e posteriormente.

> [!NOTE]
> É importante atualizar a opção AllowExtensionOperations depois de instalar manualmente o VMAgent em uma VM que foi implantada a partir da imagem sem habilitação ProvisionVMAgent.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Pré-requisitos
- O Windows VM Agent precisa de pelo menos O Windows Server 2008 R2 (64 bits) para ser executado, com o .Net Framework 4.0. Veja [o suporte à versão mínima para agentes de máquinas virtuais no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Certifique-se de que sua VM tenha acesso ao endereço IP 168.63.129.16. Para obter mais [informações, consulte o que é endereço IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Detectar o Agente de VM

### <a name="powershell"></a>PowerShell

O módulo do Azure Resource Manager PowerShell pode ser usado para recuperar informações sobre as VMs do Azure. Para obter informações sobre uma VM, como o estado de provisionamento para o Agente de VM do Azure, use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A saída de exemplo condensada a seguir mostra a propriedade *ProvisionVMAgent* aninhada dentro de *OSProfile*. Essa propriedade pode ser usada para determinar se o agente de VM foi implantado na VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O script a seguir pode ser usado para retornar uma lista concisa de nomes de VM e o estado do Agente de VM:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Detecção Manual

Quando conectado a uma VM do Windows, o Gerenciador de Tarefas pode ser usado para examinar os processos em execução. Para verificar o Agente de VM do Azure, abra o Gerenciador de Tarefas, clique na guia *Detalhes* e procure pelo nome de processo **WindowsAzureGuestAgent.exe**. A presença desse processo indica que o agente de VM está instalado.


## <a name="upgrade-the-vm-agent"></a>Atualizar o Agente de VM
O Agente de VM do Azure para Windows é atualizado automaticamente. Conforme novas VMs são implantadas no Azure, elas receberão o agente de VM mais recente em tempo de provisionamento. Imagens de VM personalizadas devem ser atualizadas manualmente para incluir o novo agente de VM em tempo de criação de imagem.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Coleção de logs automáticos do agente convidado do Windows
O Windows Guest Agent tem um recurso para coletar automaticamente alguns logs. Este recurso é controlador do processo CollectGuestLogs.exe. Existe tanto para o PaaS Cloud Services quanto para as Máquinas Virtuais IaaS e seu objetivo é coletar rapidamente & coletar automaticamente alguns registros de diagnósticos de uma VM - para que possam ser usados para análise off-line. Os registros coletados são Registros de Eventos, Registros de SO, Logs do Azure e algumas chaves de registro. Ele produz um arquivo ZIP que é transferido para o Host da VM. Este arquivo ZIP pode então ser examinado por equipes de engenharia e profissionais de suporte para investigar problemas a pedido do cliente que possui a VM.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões de VM, consulte [Visão geral de recursos e extensões de máquina virtual do Azure](overview.md).
