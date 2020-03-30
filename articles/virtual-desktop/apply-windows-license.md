---
title: Aplicar licença do Windows para hospedar máquinas virtuais de sessão - Azure
description: Descreve como aplicar a licença do Windows para VMs de desktop virtual do Windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254229"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Aplicar a licença do Windows para hospedar máquinas virtuais de hospedagem

Os clientes que estão devidamente licenciados para executar as cargas de trabalho do Windows Virtual Desktop são elegíveis para aplicar uma licença do Windows às suas máquinas virtuais host de sessão e executá-las sem pagar por outra licença. Para obter mais informações, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Maneiras de usar sua licença de Desktop Virtual do Windows
O licenciamento do Windows Virtual Desktop permite que você aplique uma licença a qualquer máquina virtual do Windows ou Windows Server que esteja registrada como um host de sessão em um pool de hosts e receba conexões de usuário. Esta licença não se aplica a máquinas virtuais que estão sendo executados como servidores de compartilhamento de arquivos, controladores de domínio e assim por diante.

Existem algumas maneiras de usar a licença de Área de Trabalho Virtual do Windows:
- Você pode criar um pool de host e suas máquinas virtuais de host de sessão usando a oferta do [Azure Marketplace](./create-host-pools-azure-marketplace.md). As máquinas virtuais criadas dessa forma automaticamente têm a licença aplicada.
- Você pode criar um pool de host e suas máquinas virtuais de host de sessão usando o [modelo gitHub Azure Resource Manager](./create-host-pools-arm-template.md). As máquinas virtuais criadas dessa forma automaticamente têm a licença aplicada.
- Você pode aplicar uma licença a uma máquina virtual host de sessão existente. Para fazer isso, primeiro siga as instruções em [Criar um pool de host com o PowerShell](./create-host-pools-powershell.md) para criar um pool de host e VMs associados e, em seguida, retornar a este artigo para aprender como aplicar a licença.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Aplicar uma licença do Windows a um VM host de sessão
Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azure/overview). Execute o seguinte cmdlet PowerShell para aplicar a licença do Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verifique se a VM do host de sessão está utilizando o benefício de licenciamento
Depois de implantar sua VM, execute este cmdlet ot verificar o tipo de licença:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Uma VM de host de sessão com a licença do Windows aplicada mostrará algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VMs sem a licença do Windows aplicada mostrarão algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Execute o cmdlet a seguir para ver uma lista de todas as VMs host de sessão que têm a licença do Windows aplicada em sua assinatura do Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
