---
title: Atualizar ou excluir um Azure Load Balancer existente usado pelo Conjunto de Dimensionamento de Máquinas Virtuais
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Com este artigo de instruções, introdução aos conjuntos de dimensionamento de máquinas virtuais e Standard Load Balancer do Azure.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790082"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Como atualizar/excluir Azure Load Balancer usados por conjuntos de dimensionamento de máquinas virtuais

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Como configurar Azure Load Balancer para escalar horizontalmente os conjuntos de dimensionamento de máquinas virtuais
  * Verifique se o Load Balancer tem o [pool de NAT de entrada](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) configurado e se o conjunto de dimensionamento de máquinas virtuais é colocado no pool de back-end do Load Balancer. Azure Load Balancer criará automaticamente novas regras NAT de entrada no pool de NAT de entrada quando novas instâncias de máquina virtual forem adicionadas ao conjunto de dimensionamento de máquinas virtuais. 
  * Para verificar se o pool NAT de entrada está configurado corretamente, 
  1. Entre no Portal do Azure em https://portal.azure.com.
  
  1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
  
  1. Em **configurações**, selecione **regras de NAT de entrada**.
Se você vir no painel direito, uma lista de regras criadas para cada instância individual no conjunto de dimensionamento de máquinas virtuais, o incerto que você está pronto para escalar verticalmente a qualquer momento.

## <a name="how-to-add-inbound-nat-rules"></a>Como adicionar regras de NAT de entrada? 
  * A regra NAT de entrada individual não pode ser adicionada. No entanto, você pode adicionar um conjunto de regras de NAT de entrada com o intervalo de portas de front-end definido e a porta de backend para todas as instâncias no conjunto de dimensionamento de máquinas virtuais.
  * Para adicionar um conjunto completo de regras de NAT de entrada para os conjuntos de dimensionamento de máquinas virtuais, primeiro você precisa criar um pool de NAT de entrada no Load Balancer e, em seguida, fazer referência ao pool de NAT de entrada do perfil de rede do conjunto de dimensionamento de máquinas virtuais. Um exemplo completo usando A CLI é mostrado abaixo.
  * O novo pool de NAT de entrada não deve ter sobreposição de intervalo de portas de front-end com pools NAT de entrada existentes. Para exibir os pools de NAT de entrada existentes configurados, você pode usar este [comando da CLI](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Como atualizar regras de NAT de entrada? 
  * A regra NAT de entrada individual não pode ser atualizada. No entanto, você pode atualizar um conjunto de regras NAT de entrada com o intervalo de portas de front-end definido e a porta de back-end para todas as instâncias no conjunto de dimensionamento de máquinas virtuais.
  * Para atualizar um conjunto completo de regras NAT de entrada para os conjuntos de dimensionamento de máquinas virtuais, você precisa atualizar o pool NAT de entrada no Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Como excluir regras de NAT de entrada? 
* A regra NAT de entrada individual não pode ser excluída. No entanto, você pode excluir o conjunto inteiro de regras de NAT de entrada.
* Para excluir o conjunto inteiro de regras de NAT de entrada usadas pelo conjunto de dimensionamento, primeiro você precisa remover o pool NAT do conjunto de dimensionamento. Um exemplo completo usando A CLI é mostrado abaixo:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Como adicionar várias configurações de IP:
1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **configurações**, selecione **configurações de IP de front-end** e selecione **Adicionar**.
   
1. Na página **Adicionar endereço IP de front-end** , digite os valores e selecione **OK**

1. Siga as [etapas 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) neste tutorial se forem necessárias novas regras de balanceamento de carga

1. Crie um novo conjunto de regras de NAT de entrada usando as configurações de IP de front-end criadas recentemente, se necessário. O exemplo pode ser encontrado aqui na [seção anterior].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Como excluir a configuração de IP de front-end usada pelo conjunto de dimensionamento de máquinas virtuais: 
 1. Para excluir a configuração de IP de front-end em uso pelo conjunto de dimensionamento, primeiro você precisa excluir o pool NAT de entrada (conjunto de regras NAT de entrada) referenciando a configuração de IP de front-end. As instruções sobre como excluir as regras de entrada podem ser encontradas na seção anterior.
 1. Exclua a regra de balanceamento de carga referenciando a configuração de IP de front-end. 
 1. Exclua a configuração de IP de front-end.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Como excluir Azure Load Balancer usado pelo conjunto de dimensionamento de máquinas virtuais: 
 1. Para excluir a configuração de IP de front-end em uso pelo conjunto de dimensionamento, primeiro você precisa excluir o pool NAT de entrada (conjunto de regras NAT de entrada) referenciando a configuração de IP de front-end. As instruções sobre como excluir as regras de entrada podem ser encontradas na seção anterior.
 
 1. Exclua a regra de balanceamento de carga que faz referência ao pool de back-end que contém o conjunto de dimensionamento
 
 1. Remova a referência de loadBalancerBackendAddressPool do perfil de rede do conjunto de dimensionamento de máquinas virtuais. Um exemplo completo usando A CLI é mostrado abaixo:
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Por fim, exclua o recurso Load Balancer.
 
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Azure Load Balancer e conjunto de dimensionamento de máquinas virtuais, leia mais sobre os conceitos.

> [Azure Load Balancer com conjuntos de dimensionamento de máquinas virtuais do Azure](load-balancer-standard-virtual-machine-scale-sets.md)