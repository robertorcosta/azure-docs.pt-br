---
title: Crie uma VM Azure com rede acelerada - Azure PowerShell
description: Saiba como criar uma máquina virtual Linux com Rede Acelerada.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245051"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Crie uma máquina virtual do Windows com rede acelerada usando o Azure PowerShell

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Windows com Rede Acelerada. Para criar uma VM Linux com Rede Acelerada, consulte [Criar uma VM Linux com Rede Acelerada](create-vm-accelerated-networking-cli.md). Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM compatíveis. A figura abaixo mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre switches virtuais, consulte [virtualização da rede Hyper-V e switch virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com Rede Acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios da rede acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="limitations-and-constraints"></a>Limitações e Restrições

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
As seguintes distribuições têm suporte imediato da Galeria do Azure:
* **Windows Server 2016 Datacenter** 
* **Centro de dados R2 do Windows Server 2012**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Instâncias de VM compatíveis
A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/Fs

Em instâncias que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 4 ou mais vCPUs. As séries suportadas são: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre instâncias de VM, consulte [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões do Azure públicas e na Nuvem do Azure Governamental.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitando Rede Acelerada em uma VM em execução
Um tamanho de VM com suporte sem rede acelerada habilitada só pode ter o recurso habilitado quando ele for interrompido e desalocado.

### <a name="deployment-through-azure-resource-manager"></a>Implantação por meio do Azure Resource Manager
Máquinas virtuais (clássicas) não podem ser implantadas com Rede Acelerada.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Criar uma VM do Windows com Rede Acelerada do Azure
## <a name="portal-creation"></a>Criação de portal
Embora este artigo forneça etapas para criar uma máquina virtual com rede acelerada usando o Azure Powershell, você também pode [criar uma máquina virtual com rede acelerada usando o portal Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, na **Criar uma lâmina de máquina virtual,** escolha a guia **Rede.**  Nesta guia, há uma opção para **rede acelerada**.  Se você tiver escolhido um [sistema operacional suportado](#supported-operating-systems) e [tamanho VM,](#supported-vm-instances)esta opção será preenchida automaticamente para "On".  Caso assim, ele preencherá a opção "Off" para Rede Acelerada e dará ao usuário uma razão pela qual ele não está habilitado.   
* *Nota:* Somente sistemas operacionais suportados podem ser habilitados através do portal.  Se você estiver usando uma imagem personalizada e sua imagem suportar a Rede Acelerada, crie sua VM usando CLI ou Powershell. 

Depois que a máquina virtual for criada, você pode confirmar que a Rede Acelerada está ativada seguindo as instruções no Confirmar que a rede acelerada está ativada.

## <a name="powershell-creation"></a>Criação de powershell
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale [o Azure PowerShell](/powershell/azure/install-az-ps) versão 1.0.0 ou posterior. Para localizar a versão atualmente instalada, execute `Get-Module -ListAvailable Az`. Se você precisar instalar ou atualizar, instale a versão mais recente do módulo Az na [Galeria PowerShell](https://www.powershellgallery.com/packages/Az). Em uma sessão powerShell, faça login em uma conta do Azure usando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes dos parâmetros de exemplo incluíam *myResourceGroup,* *myNic*e *myVM*.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *centralus*:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Primeiro, crie uma configuração de sub-rede com [o New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O exemplo a seguir cria uma sub-rede chamada *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Crie uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), com a sub-rede *mySubnet.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Primeiro, crie uma regra de grupo de segurança de rede com [new-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Crie um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua a regra de segurança *Allow-RDP-All* a ele. Além da regra *Allow-RDP-All*, o grupo de segurança de rede contém várias regras padrão. Uma regra padrão desabilita todo o acesso de entrada proveniente da Internet, razão pela qual a regra *Allow-RDP-All* é atribuída ao grupo de segurança de rede para que você possa se conectar remotamente à máquina virtual quando ela for criada.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associe o grupo de segurança da rede à sub-rede *mySubnet* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança de rede é eficaz para todos os recursos implantados na sub-rede.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Criar um adaptador de rede com rede acelerada
Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Você não precisa de um endereço IP público se não planeja acessar a máquina virtual por meio da Internet, mas precisa para concluir as etapas deste artigo.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Crie uma interface de rede com [a New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com rede acelerada ativada e atribua o endereço IP público à interface da rede. O exemplo a seguir cria um adaptador de rede denominado *myNic* na sub-rede *mySubnet* da rede virtual *myVnet* e atribui o endereço IP público *myPublicIp* a ele:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Defina suas credenciais de VM como a variável `$cred` usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Primeiro, defina sua VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O exemplo a seguir define uma VM denominada *myVM* com um tamanho de VM que é compatível com Rede Acelerada (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Para obter uma lista de todos os tamanhos e características de VM, consulte [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Crie o restante da configuração da VM com [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O exemplo a seguir cria uma VM do Windows Server 2016:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Anexar a interface de rede que você criou anteriormente com [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Finalmente, crie sua VM com [o New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confirmar se o driver está instalado no sistema operacional

Depois de criar a VM no Azure, conecte-se à VM e verifique se o driver está instalado no Windows.

1. Em um navegador da Internet, abra o [Portal](https://portal.azure.com) do Azure e entre com a sua conta do Azure.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do Portal do Azure, digite *myVm*. Quando **myVm** aparecer nos resultados da pesquisa, clique nela. Se **Criando** está visível sob o botão **Conectar**, o Azure ainda não terminou de criar a VM. Clique em **Conectar** no canto superior esquerdo da visão geral somente depois que **Criando** não for mais exibido sob o botão **Conectar**.
3. Insira o nome de usuário e senha que você inseriu em [Criar a máquina virtual](#create-the-virtual-machine). Se você nunca se conectou a uma VM do Windows no Azure, consulte [Conectar-se a máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Clique com o botão direito do mouse em Iniciar do Windows e clique em **Gerenciador de Dispositivos**. Expanda o nó **Adaptadores de rede**. Verifique se o **Adaptador de Ethernet de Função Virtual Mellanox ConnectX-3** aparece, como mostrado na figura a seguir:

    ![Gerenciador de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

A Rede Acelerada agora está habilitada para sua VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Habilitar Rede Acelerada em VMs existentes
Se você tiver criado uma VM sem Rede Acelerada, será possível habilitar esse recurso em uma VM existente.  A VM deve dar suporte à Rede Acelerada atendendo aos pré-requisitos a seguir que também estão descritos acima:

* A VM deve ter um tamanho com suporte para Rede Acelerada
* A VM deve ser uma imagem da Galeria do Azure com suporte (e a versão de kernel do Linux)
* Todas as VMs em um conjunto de disponibilidade ou VMSS devem ser interrompidas/desalocadas antes de se habilitar a Rede Acelerada em uma NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais e VMs em um conjunto de disponibilidade
Primeiro interrompa/desaloque a VM ou, se for um Conjunto de Disponibilidade, todas as VMs no conjunto:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Importante: observe que se sua VM tiver sido criada individualmente, sem um conjunto de disponibilidade, você só precisará interromper/desalocar a VM individual para habilitar a Rede Acelerada.  Se sua VM tiver sido criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade precisarão ser interrompidas/desalocadas antes de habilitar a Rede Acelerada em qualquer uma das NICs. 

Uma vez interrompida, habilite a Rede Acelerada na NIC de sua VM:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Reinicie sua VM ou, se em um conjunto de disponibilidade, todas as VMs no conjunto e confirme se a rede acelerada está ativada:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
O VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Em primeiro lugar, interrompa as VMs:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Depois que as VMs forem interrompidas, atualize a propriedade de Rede Acelerada na interface de rede:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Observe que um VMSS tem upgrades de VM que aplicam atualizações usando três diferentes configurações: automática, sem interrupção e manual.  Nessas instruções, a política é definida como automática para que o VMSS acompanhe as alterações imediatamente após a reinicialização.  Para defini-la como automática para que as alterações sejam aplicadas imediatamente:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Finalmente, reinicie o VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Após você reiniciar, aguarde o término dos upgrades. Após a conclusão, o VF será exibido dentro da VM.  (Verifique se você está usando um tamanho de VM e um sistema operacional com suporte)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar VMs existentes com Rede Acelerada

VMs com Rede Acelerada habilitada só podem ser redimensionadas para VMs com suporte para Rede Acelerada.  

Uma VM com Rede Acelerada habilitada não pode ser redimensionada para uma instância de VM que não oferece suporte a Rede Acelerada usando a operação de redimensionamento.  Em vez disso, para redimensionar uma dessas VMs:

* Interrompa/desaloque a VM ou, em um conjunto de disponibilidade/VMSS, interrompa/desaloque todas as VMs no conjunto/VMSS.
* A Rede Acelerada deve ser desabilitada na NIC da VM ou, se em um conjunto de disponibilidade/VMSS, todas as VMs no conjunto/VMSS.
* Quando a Rede Acelerada é desabilitada, a VM/o conjunto de disponibilidade/o VMSS podem ser movidos para um novo tamanho que não oferece suporte para Rede Acelerada e reiniciados.
