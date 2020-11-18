---
title: 'Início Rápido: Criar um balanceador de carga interno – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga interno usando o Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696771"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Início Rápido: Criar um balanceador de carga interno para balancear a carga de VMs usando o Azure PowerShell

Comece a usar o Azure Load Balancer usando o Azure PowerShell para criar um balanceador de carga interno e duas máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* **CreateIntLBQS-rg** nomeado.
* Na localização **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Criar uma rede virtual e um host do Azure Bastion

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Chamada **myVNet**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Sub-rede chamada **myBackendSubnet**.
* Rede virtual **10.0.0.0/16**.
* Sub-rede **10.0.0.0/24**.
* Sub-rede chamada **AzureBastionSubnet**.
* Sub-rede **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Criar o endereço IP público para o host do Azure Bastion

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público para o bastion host:

* Chamado de **myPublicIPBastion**
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Método de alocação **estático**.
* SKU **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Criar um host do Azure Bastion

Use [New-AzBastion](/powershell/module/az.network/new-azbastion) para criar um bastion host:

* Chamado de **myBastion**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Associado ao endereço IP público **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede
Crie um grupo de segurança de rede para definir conexões de entrada para sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra do grupo de segurança de rede para a porta 80
Crie uma regra do grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Chamada **myNSGRuleHTTP**.
* Descrição **Permitir HTTP**.
* Acesso **Permitir**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Prioridade **2000**.
* Origem da **Internet**.
* Intervalo de portas de origem de **(*)** .
* Prefixo do endereço de destino de **(*)** .
* **Porta 80** de destino.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Com as regras de segurança criadas nas etapas anteriores armazenadas em uma variável.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Criar Standard Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-frontend-ip"></a>Criar um IP de front-end

Crie um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Chamado **myFrontEnd**.
* O endereço IP privado de **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurar o pool de endereços de back-end

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Chamado **myBackEndPool**.
* As VMs são anexadas a este pool de back-end nas etapas restantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.
* Habilitar redefinição de TCP.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>As máquinas virtuais do pool de back-end não terão conectividade de saída com a Internet com essa configuração. </br> Para obter mais informações sobre como fornecer a conectividade de saída, confira: </br> **[Conexões de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga somente de saída](egress-only.md)** </br> **[O que é NAT de Rede Virtual?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Criar recursos do balanceador de carga

Crie um balanceador de carga interno com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Chamado **myLoadBalancer**
* Na região **eastus**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Criar interfaces de rede

Crie três interfaces de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Chamada **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.
* Anexada ao balanceador de carga **myLoadBalancer** em **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Chamada **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.
* Anexada ao balanceador de carga **myLoadBalancer** em **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie as máquinas virtuais com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Anexada ao balanceador de carga **myLoadBalancer**.
* Na **Zona 1**.
* Na localização **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Anexada ao balanceador de carga **myLoadBalancer**.
* Na **Zona 2**.
* Na localização **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Criar uma rede virtual e um host do Azure Bastion

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Chamada **myVNet**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Sub-rede chamada **myBackendSubnet**.
* Rede virtual **10.0.0.0/16**.
* Sub-rede **10.0.0.0/24**.
* Sub-rede chamada **AzureBastionSubnet**.
* Sub-rede **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Criar o endereço IP público para o host do Azure Bastion

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público para o bastion host:

* Chamado de **myPublicIPBastion**
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Método de alocação **estático**.
* SKU **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Criar um host do Azure Bastion

Use [New-AzBastion](/powershell/module/az.network/new-azbastion) para criar um bastion host:

* Chamado de **myBastion**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Associado ao endereço IP público **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Levará alguns minutos para que o host do Azure Bastion seja implantado.


### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede
Crie um grupo de segurança de rede para definir conexões de entrada para sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra do grupo de segurança de rede para a porta 80
Crie uma regra do grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Chamada **myNSGRuleHTTP**.
* Descrição **Permitir HTTP**.
* Acesso **Permitir**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Prioridade **2000**.
* Origem da **Internet**.
* Intervalo de portas de origem de **(*)** .
* Prefixo do endereço de destino de **(*)** .
* **Porta 80** de destino.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Com as regras de segurança criadas nas etapas anteriores armazenadas em uma variável.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-frontend-ip"></a>Criar um IP de front-end

Crie um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Chamado **myFrontEnd**.
* O endereço IP privado de **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurar o pool de endereços de back-end

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Chamado **myBackEndPool**.
* As VMs são anexadas a este pool de back-end nas etapas restantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Criar recursos do balanceador de carga

Crie um balanceador de carga público com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Chamado **myLoadBalancer**
* Na região **eastus**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Criar interfaces de rede

Crie três interfaces de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Chamada **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.
* Anexada ao balanceador de carga **myLoadBalancer** em **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Chamada **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.
* Anexada ao balanceador de carga **myLoadBalancer** em **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Use [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) para criar um conjunto de disponibilidade para as máquinas virtuais:

* Chamado **myAvSet**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie as máquinas virtuais com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Anexada ao balanceador de carga **myLoadBalancer**.
* Na localização **eastus**.
* No conjunto de disponibilidade **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Anexada ao balanceador de carga **myLoadBalancer**.
* Na localização **eastus**.
* No conjunto de disponibilidade **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

São necessários alguns minutos para criar e configurar as três VMs.

---

## <a name="install-iis"></a>Instalar o IIS

Use [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest) para instalar a extensão de script personalizado. 

A extensão executa o cdmlet Add-WindowsFeature Web-Server do PowerShell para instalar o servidor Web do IIS e atualiza a página Default.htm para mostrar o nome do host da VM:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-network-interface"></a>Criar adaptador de rede

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* Chamado **myNicTestVM**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na localização **eastus**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Criar máquina virtual

Defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie a máquina virtual com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Chamada **myTestVM**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicTestVM**.
* Na localização **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Teste

1. [Entre](https://portal.azure.com) no portal do Azure.

1. Encontre o endereço IP privado para o balanceador de carga na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer**.

2. Anote ou copie o endereço ao lado de **Endereço IP Privado** na **Visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, em seguida, **Todos os recursos** e, na lista de recursos, escolha **myTestVM**, que está no grupo de recursos **CreateIntLBQS-rg**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Insira o endereço IP da etapa anterior na barra de endereços do navegador. A página padrão do servidor Web do IIS é exibida no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Criar um balanceador de carga interno Standard" border="true":::
   
Para ver o balanceador de carga distribuir o tráfego entre as três VMs, personalize a página padrão do servidor Web do IIS de cada VM e force a atualização do navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o balanceador de carga e os recursos restantes.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido

* Você criou um balanceador de carga interno standard ou básico
* Anexou máquinas virtuais. 
* Configurou a regra de tráfego e a investigação de integridade do balanceador de carga.
* Testou o balanceador de carga.

Para saber mais sobre o Azure Load Balancer, vá para...
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)