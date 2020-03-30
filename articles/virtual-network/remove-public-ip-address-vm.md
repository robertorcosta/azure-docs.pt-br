---
title: Dissociar um endereço IP público de uma VM Azure
titlesuffix: Azure Virtual Network
description: Saiba como dissociar um endereço IP público de uma VM
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900752"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Dissociar um endereço IP público de uma VM Azure 

Neste artigo, você aprende como dissociar um endereço IP público de uma máquina virtual Azure (VM).

Você pode usar o [portal Azure](#azure-portal), a [interface de linha de comando](#azure-cli) Azure (CLI) ou [powerShell](#powershell) para dissociar um endereço IP público de uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue ou procure a máquina virtual da que deseja desassociar o endereço IP público e, em seguida, selecione-o.
3. Na página VM, selecione **Visão geral,** selecione o endereço IP público como mostrado na imagem a seguir:

   ![Selecione IP público](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na página de endereço IP público, selecione **Visão geral**e selecione **Dissociado,** conforme mostrado na imagem a seguir:

    ![Disciar IP público](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Em **Dissociar endereço IP público,** selecione **Sim**.

## <a name="azure-cli"></a>CLI do Azure

Instale [o Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **Tente nos** comandos CLI a seguir. Selecionando **Try ele** invoca uma Cloud Shell com a sua conta do Azure.

1. Se estiver usando a CLI localmente em Bash, entre no Azure com `az login`.
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a uma VM. Use o comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) para dissociar um endereço IP público de uma configuração IP. O exemplo a seguir dissocia um endereço IP público chamado *myVMPublicIP* da configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVVMNic* que está anexada a um VM chamado *myVM* em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se você não sabe o nome de uma interface de rede anexada à sua VM, use o comando [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se você não sabe o nome de uma configuração IP para uma interface de rede, use o comando [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações ip públicas para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se você não sabe o nome de uma configuração ip pública para uma interface de rede, use o comando [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações ip públicas para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Instale [o PowerShell](/powershell/azure/install-az-ps)ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o botão **Tentar nos** comandos PowerShell a seguir. Selecionando **Try ele** invoca uma Cloud Shell com a sua conta do Azure.

1. Se estiver usando o PowerShell localmente, `Connect-AzAccount`faça login no Azure com .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a uma VM. Use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede. Defina o valor do endereço IP público como nulo e use o comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para gravar a nova configuração IP na interface da rede.

   O exemplo a seguir dissocia um endereço IP público chamado *myVMPublicIP* de uma interface de rede chamada *myVVVMNic* que está anexada a uma VM chamada *myVM*. Todos os recursos estão em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se você não sabe o nome de uma interface de rede anexada à sua VM, use o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se você não sabe o nome de uma configuração IP para uma interface de rede, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [associar um endereço IP público a uma VM](associate-public-ip-address-vm.md).
