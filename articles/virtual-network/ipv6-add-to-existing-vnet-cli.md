---
title: Adicionar IPv6 a um aplicativo IPv4 na rede virtual Azure - Azure CLI
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 em um aplicativo existente na rede virtual Azure usando o Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f3f9b32ea55f0ceebf08b22ccc7e2ceec0b6227e
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420797"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Adicionar IPv6 a um aplicativo IPv4 na rede virtual Azure - Azure CLI

Este artigo mostra como adicionar endereços IPv6 a um aplicativo que está usando o endereço IP público IPv4 em uma rede virtual Azure para um Balanceador de Carga Padrão usando o Azure CLI. A atualização no local inclui uma rede virtual e uma sub-rede, um Balancer de Carga Padrão com configurações frontend IPv4 + IPV6, VMs com NICs que possuem configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este guia de início rápido exigirá a versão 2.0.28 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você implantou um Balancer de Carga Padrão como descrito no [Quickstart: Crie um Balancer de Carga Padrão - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Criar endereços IPv6

Crie um endereço IPv6 público com [a rede az public-ip criar](/cli/azure/network/public-ip) para o seu Balanceador de Carga Padrão. O exemplo a seguir cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configure o frontend do balanceador de carga IPv6

COnfigure o balanceador de carga com o novo endereço IP IPv6 usando [a rede az lb frontend-ip criar](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) da seguinte forma:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configurar o pool de backend do balanceador de carga IPv6

Crie o pool de back-end para NICs com endereços IPv6 usando [a rede az lb address-pool criar](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) da seguinte forma:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configure as regras do balanceador de carga IPv6

Crie regras de balanceador de carga IPv6 com [a criação da regra az network lb](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicionar faixas de endereço IPv6 à rede virtual e à sub-rede que hospeda o balanceador de carga da seguinte forma:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Adicionar configuração IPv6 aos NICs

Configure os NICs VM com um endereço IPv6 usando [a rede az nic ip-config criar](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) da seguinte forma:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal Azure
Você pode visualizar a rede virtual iPv6 dual stack no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, digite *myVnet*.
2. Quando **o myVnet** aparecer nos resultados da pesquisa, selecione-o. Isso lança a página **Visão Geral** da rede virtual dual stack chamada *myVNet*. A rede virtual dual stack mostra os três NICs com as configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mySubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você atualizou um Balancer de Carga Padrão existente com uma configuração IP frontend IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Você também adicionou configurações IPv6 aos NICs das VMs no pool de backend. Para saber mais sobre o suporte ao IPv6 nas redes virtuais do Azure, veja [o que é IPv6 para Azure Virtual Network?](ipv6-overview.md)
