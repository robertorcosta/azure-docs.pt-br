---
title: Associar um endereço IP público a uma máquina virtual
titlesuffix: Azure Virtual Network
description: Aprenda a associar um endereço IP público a uma máquina virtual.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647451"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associar um endereço IP público a uma máquina virtual

Neste artigo, você aprende a associar um endereço IP público a uma máquina virtual (VM) existente. Se você quiser se conectar a uma VM a partir da internet, a VM deve ter um endereço IP público associado a ele. Se você quiser criar uma nova VM com um endereço IP público, você pode fazê-lo usando o [portal Azure,](virtual-network-deploy-static-pip-arm-portal.md)a [interface de linha de comando (CLI) do Azure](virtual-network-deploy-static-pip-arm-cli.md)ou [o PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Endereços IP públicos têm um valor nominal. Para detalhes, consulte o [preço](https://azure.microsoft.com/pricing/details/ip-addresses/). Há um limite para o número de endereços IP públicos que você pode usar por assinatura. Para obter detalhes, consulte [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Você pode usar o [portal Azure](#azure-portal), a [interface de linha de comando](#azure-cli) Azure (CLI) ou [powerShell](#powershell) para associar um endereço IP público a uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue ou procure a máquina virtual à que deseja adicionar o endereço IP público e, em seguida, selecione-o.
3. Em **Configurações,** **selecione Rede**e selecione a interface de rede a que deseja adicionar o endereço IP público, conforme mostrado na imagem a seguir:

   ![Selecione interface de rede](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Os endereços IP públicos estão associados a interfaces de rede anexadas a uma VM. Na imagem anterior, a VM só tem uma interface de rede. Se a VM tivesse várias interfaces de rede, todas elas apareceriam, e você selecionaria a interface de rede a que deseja associar o endereço IP público.

4. Selecione **configurações IP** e selecione uma configuração IP, conforme mostrado na imagem a seguir:

   ![Selecione configuração IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Os endereços IP públicos estão associados a configurações IP para uma interface de rede. Na imagem anterior, a interface de rede tem uma configuração IP. Se a interface de rede tivesse várias configurações de IP, todas elas apareceriam na lista e você selecionaria a configuração IP à que deseja associar o endereço IP público.

5. Selecione **Ativado**e selecione **endereço IP *(Configure as configurações necessárias).*** Escolha um endereço IP público existente, que fecha automaticamente a caixa **de endereço IP público Choose.** Se você não tiver nenhum endereço IP público disponível listado, você precisa criar um. Para saber como, consulte [Criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). Selecione **Salvar,** como mostrado na imagem a seguir e, em seguida, feche a caixa para a configuração IP.

   ![Habilite o endereço IP público](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Os endereços IP públicos que aparecem são aqueles que existem na mesma região que a VM. Se você tiver vários endereços IP públicos criados na região, todos aparecerão aqui. Se algum está acinzentado, é porque o endereço já está associado a um recurso diferente.

6. Exibir o endereço IP público atribuído à configuração IP, conforme mostrado na imagem a seguir. Pode levar alguns segundos para que um endereço IP apareça.

   ![Exibir endereço IP público atribuído](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > O endereço é atribuído a partir de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [As faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas usadas para a região. Se você precisar que o endereço seja atribuído a partir de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

7. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="azure-cli"></a>CLI do Azure

Instale [o Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **Tente nos** comandos CLI a seguir. Selecionando **Try ele** invoca uma Cloud Shell com a sua conta do Azure.

1. Se estiver usando a CLI localmente em Bash, entre no Azure com `az login`.
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a uma VM. Use o comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) para associar um endereço IP público a uma configuração IP. O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVVMNic* que existe em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se você não tiver um endereço IP público existente, use o comando [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um. Por exemplo, o comando a seguir cria um endereço IP público chamado *myVMPublicIP* em um grupo de recursos chamado *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que você pode querer personalizar. Para saber mais sobre todas as configurações públicas de endereço IP, consulte [Criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [As faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não sabe o nome de uma interface de rede anexada à sua VM, use o comando [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se você não sabe o nome de uma configuração IP para uma interface de rede, use o comando [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Exibir o endereço IP público atribuído à configuração IP com o comando [az vm list-ip-addresses.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) O exemplo a seguir mostra os endereços IP atribuídos a uma VM existente chamada *myVM* em um grupo de recursos chamado *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [As faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas usadas para a região. Se você precisar que o endereço seja atribuído a partir de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="powershell"></a>PowerShell

Instale [o PowerShell](/powershell/azure/install-az-ps)ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o botão **Tentar nos** comandos PowerShell a seguir. Selecionando **Try ele** invoca uma Cloud Shell com a sua conta do Azure.

1. Se estiver usando o PowerShell localmente, `Connect-AzAccount`faça login no Azure com .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a uma VM. Use os comandos [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) para obter a rede virtual e a sub-rede em que a interface de rede está. Em seguida, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede e o comando [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter um endereço IP público existente. Em seguida, use o comando [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) para associar o endereço IP público à configuração IP e ao comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para gravar a nova configuração IP na interface de rede.

   O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVVMNic* que existe em uma sub-rede chamada *myVMSubnet* em uma rede virtual chamada *myVMVNet*. Todos os recursos estão em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se você não tiver um endereço IP público existente, use o comando [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) para criar um. Por exemplo, o comando a seguir cria um endereço IP público *dinâmico* chamado *myVMPublicIP* em um grupo de recursos chamado *myResourceGroup* na região *de Eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que você pode querer personalizar. Para saber mais sobre todas as configurações públicas de endereço IP, consulte [Criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [As faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não sabe o nome de uma interface de rede anexada à sua VM, use o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se você não sabe o nome da rede virtual ou da sub-rede em que a interface de rede está, use o `Get-AzNetworkInterface` comando para visualizar as informações. Por exemplo, o comando a seguir obtém as informações de rede e sub-rede virtuais para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVNET* é o nome da rede virtual e *myVMSubnet* é o nome da sub-rede.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se você não sabe o nome de uma configuração IP para uma interface de rede, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Exibir o endereço IP público atribuído à configuração IP com o comando [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) O exemplo a seguir mostra o endereço atribuído a um endereço IP público chamado *myVMPublicIP* em um grupo de recursos chamado *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se você não souber o nome do endereço IP público atribuído a uma configuração IP, execute os seguintes comandos para obtê-lo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMPublicIP* é o nome do endereço IP público atribuído à configuração IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [As faixas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas usadas para a região. Se você precisar que o endereço seja atribuído a partir de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego de rede para a VM

Antes de se conectar ao endereço IP público da internet, certifique-se de que você tenha as portas necessárias abertas em qualquer grupo de segurança de rede que você possa ter associado à interface de rede, à sub-rede em que a interface da rede está ou ambos. Embora os grupos de segurança filtrem o tráfego para o endereço IP privado da interface de rede, uma vez que o tráfego de internet de entrada chegue ao endereço IP público, o Azure traduz o endereço público para o endereço IP privado, portanto, se um grupo de segurança de rede impedir o fluxo de tráfego, a comunicação com o endereço IP público falha. Você pode visualizar as regras de segurança eficazes para uma interface de rede e sua sub-rede usando o [Portal,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)ou [PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Próximas etapas

Permita tráfego de internet de entrada em sua VM com um grupo de segurança de rede. Para saber como criar um grupo de segurança de rede, consulte [Trabalhar com grupos de segurança de rede](manage-network-security-group.md#work-with-network-security-groups). Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](security-overview.md).
