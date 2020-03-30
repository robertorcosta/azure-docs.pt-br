---
title: Adicionar ou remover uma delegação de sub-rede em uma rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar ou remover uma sub-rede delegada para um serviço no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201859"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Adicionar ou remover uma delegação de sub-rede

A delegação de sub-rede dá permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede, usando um identificador exclusivo ao implantar o serviço. Este artigo descreve como adicionar ou remover uma sub-rede delegada para um serviço Azure.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você cria uma rede virtual e a sub-rede que mais tarde será delegada a um serviço Do Zure.

1. No lado superior esquerdo da tela, selecione **Criar uma rede** > virtual de rede**de** > **recursos**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Digite *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.0.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **EastUS**.|
    | Sub-rede – Nome | Insira *mySubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.0.0.0/24*. |
    |||
1. Deixe o resto como padrão e, em seguida, selecione **Criar**.

### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que gostaria de delegar a um `Microsoft.Network/virtualNetworks/subnets/write`serviço Azure, você precisa da seguinte permissão: .

A função [contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegue uma sub-rede para um serviço Azure

Nesta seção, você delega a sub-rede criada na seção anterior a um serviço Azure.

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **Sub-redes,** em **CONFIGURAÇÕES**e selecione **mySubnet**.
4. Na página *mySubnet,* para a lista **de delegação da Subnet,** selecione entre os serviços listados na **sub-rede Delegado para um serviço** (por exemplo, **Microsoft.DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remova a delegação da sub-rede de um serviço azure

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **Sub-redes,** em **CONFIGURAÇÕES**e selecione **mySubnet**.
4. Na página *mySubnet,* para a lista **de delegação da Subnet,** selecione **Nenhum** dos serviços listados na **sub-rede Delegado para um serviço**. 

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o Azure CLI localmente, este artigo exige que você use a versão 2.0.28 do Azure CLI ou posterior. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na localização **eastus:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** no **myResourceGroup** usando [a rede vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que gostaria de delegar a um `Microsoft.Network/virtualNetworks/subnets/write`serviço Azure, você precisa da seguinte permissão: .

A função [contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegue uma sub-rede para um serviço Azure

Nesta seção, você delega a sub-rede criada na seção anterior a um serviço Azure. 

Use [a atualização da sub-rede vnet da rede az](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) para atualizar a sub-rede chamada **mySubnet** com uma delegação para um serviço Azure.  Neste **exemplo, o Microsoft.DBforPostgreSQL/serversv2** é usado para a delegação de exemplo:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Para verificar se a delegação foi aplicada, use [a sub-rede vnet az .](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Verifique se o serviço é delegado à sub-rede o serviço de **propriedadeNome**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remova a delegação da sub-rede de um serviço azure

Use [a atualização da sub-rede vnet](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) da rede az para remover a delegação da sub-rede chamada **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Para verificar se a delegação foi removida, use [a rede az vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Verifique se o serviço é removido da sub-rede o serviço de **propriedadeName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A saída do comando é um suporte nulo:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Conectar-se ao Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Criar rede virtual

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [new-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroup** usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereço IP para a rede virtual é **10.0.0.0/16**. A sub-rede dentro da rede virtual é **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que gostaria de delegar a um `Microsoft.Network/virtualNetworks/subnets/write`serviço Azure, você precisa da seguinte permissão: .

A função [contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegue uma sub-rede para um serviço Azure

Nesta seção, você delega a sub-rede criada na seção anterior a um serviço Azure. 

Use [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) para atualizar a sub-rede chamada **mySubnet** com uma delegação chamada **myDelegation** para um serviço Azure.  Neste **exemplo, o Microsoft.DBforPostgreSQL/serversv2** é usado para a delegação de exemplo:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Use [get-azdelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) para verificar a delegação:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remova a delegação da sub-rede de um serviço azure

Use [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) para remover a delegação da sub-rede chamada **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Use [get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) para verificar se a delegação foi removida:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Próximas etapas
- Aprenda a [gerenciar sub-redes no Azure](virtual-network-manage-subnet.md).
