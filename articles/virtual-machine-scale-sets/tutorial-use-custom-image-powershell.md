---
title: Tutorial – Usar uma imagem de VM personalizada em um conjunto de dimensionamento com o Azure PowerShell
description: Aprenda a usar o Azure PowerShell para criar uma imagem de VM personalizada que pode ser usada para implantar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: daef03b411a451fc3e5b73e46091672810b0f9bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278286"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: criar e usar discos uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. Para reduzir a quantidade de tarefas depois que as instâncias de VM forem implantadas, é possível usar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui todas as instalações ou configurações de aplicativo necessárias. Todas as instâncias de VM criadas no conjunto de dimensionamento usam a imagem de VM personalizada e estão prontas para atender ao tráfego do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada da VM de origem
> * Implantar um conjunto de dimensionamento que usa a imagem de VM personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Criar e configurar uma VM de origem

>[!NOTE]
> Este tutorial analisa o processo de criação e uso de uma imagem de VM generalizada. Não há suporte para criar um conjunto de dimensionamento a partir de um VHD especializado.

Primeiro, crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), depois crie uma VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Essa VM é usada como a origem para uma imagem de VM personalizada. O exemplo a seguir cria uma VM chamada *myCustomVM* no grupo de recursos chamado *myResourceGroup*. Quando solicitado, insira um nome de usuário e senha a serem usados como credenciais de logon para a VM:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Para se conectar à sua VM, liste o endereço IP público com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) da seguinte maneira:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Crie uma conexão remota com a VM. Caso use o Azure Cloud Shell, execute esta etapa de um prompt do PowerShell local ou do Cliente da Área de Trabalho Remota. Forneça seu próprio endereço IP do comando anterior. Quando solicitado, insira as credenciais usadas quando você criou a VM na primeira etapa:

```powershell
mstsc /v:<IpAddress>
```

Para personalizar sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implantada, terá todos os pacotes necessários para executar um aplicativo Web. Abra um prompt do PowerShell local na VM e instale o servidor Web do IIS com o [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) da seguinte maneira:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

A etapa final para preparar sua VM para usar como uma imagem personalizada é generalizar a VM. O Sysprep remove todas as suas informações pessoais de conta e configurações e redefine a VM para um estado limpo para implantações futuras. Para obter mais informações, consulte [Como usar o Sysprep: uma introdução](https://technet.microsoft.com/library/bb457073.aspx).

Para generalizar a VM, execute o Sysprep e defina a VM para uma experiência pronta para uso. Ao terminar, instrua o Sysprep para desligar a VM:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

A conexão remota à VM é automaticamente fechada quando o Sysprep completa o processo e a VM é desligada.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Criar uma imagem de VM personalizada da VM de origem
Agora a VM de origem está personalizada com o servidor Web do IIS instalado. Vamos criar a imagem de VM personalizada para usar com um conjunto de dimensionamento.

Para criar uma imagem, a VM precisará ser desalocada. Desaloque a VM com [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Depois, defina o estado da VM como generalizado usando [Set-AzVm](/powershell/module/az.compute/set-azvm) para que a plataforma do Azure saiba que a VM está pronta para usar uma imagem personalizada. Você só pode criar uma imagem usando uma VM generalizada:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Pode levar alguns minutos para desalocar e generalizar a VM.

Agora, crie uma imagem da VM usando [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](/powershell/module/az.compute/new-azimage). O exemplo abaixo cria uma imagem chamada *myImage* da sua VM:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```

## <a name="configure-the-network-security-group-rules"></a>Configurar as Regras do Grupo de Segurança de Rede
Antes de criar o conjunto de dimensionamento, precisamos configurar as regras do Grupo de Segurança de Rede associadas para permitir o acesso a HTTP, RDP e comunicação remota 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$rule2 = New-AzNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389

$rule3 = New-AzNetworkSecurityRuleConfig -Name remoting-rule -Description "Allow PS Remoting" -Access Allow -Protocol Tcp -Direction Inbound -Priority 120 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 5985

New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName "myResourceGroup" -Location "EastUS" -SecurityRules $rule1,$rule2,$rule3
```

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Agora crie um conjunto de dimensionamento usando [New-AzVmss](/powershell/module/az.compute/new-azvmss), que usa o parâmetro `-ImageName` para definir a imagem de VM personalizada criada na etapa anterior. Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985. Quando solicitado, forneça suas próprias credenciais administrativas desejadas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNSG"
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver seu conjunto de dimensionamento em ação, obtenha o endereço IP público do seu balanceador de carga usando [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) da seguinte maneira:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Digite o endereço IP público no seu navegador da Web. A página da Web padrão do IIS é exibida conforme mostrado no exemplo a seguir:

![IIS sendo executado a partir de uma imagem de VM personalizada](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e os recursos adicionais, exclua o grupo de recursos e todos os seus recursos com [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar e usar uma imagem de VM personalizada para seus conjuntos de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada
> * Implantar um conjunto de dimensionamento que usa a imagem de VM personalizada

Siga para o próximo tutorial para saber como implantar aplicativo ao seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implantar aplicativos em seus conjuntos de dimensionamento](tutorial-install-apps-powershell.md)
