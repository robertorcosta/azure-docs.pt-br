---
title: Crie um serviço de Azure Private Link usando o Azure CLI
description: Saiba como criar um serviço de Azure Private Link usando o Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350232"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Crie um serviço de Private Link usando o Azure CLI
Este artigo mostra como criar um serviço de Private Link no Azure usando o Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o Azure CLI localmente, este quickstart exige que você use a versão CLI mais recente do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para hospedá-la. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *westcentralus*:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Criar uma sub-rede
Crie uma sub-rede para a rede virtual com [a rede az vnet criar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Este exemplo cria uma sub-rede chamada *mySubnet* na rede virtual *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Criar um balanceador de carga interna 
Crie um balanceador de carga interna com [a rede az lb create](/cli/azure/network/lb#az-network-lb-create). Este exemplo cria um balanceador de carga interna chamado *myILB* no grupo de recursos chamado *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Criar uma investigação de integridade do balanceador de carga

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode receber o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma sonda de saúde com [a sonda AZ Network lb criar](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorar a saúde das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myHTTPRule* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no pool de front-end *myFrontEnd* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, não cobrimos a criação de máquinas virtuais. Você pode seguir as etapas em [Criar um balanceador de carga interna para carregar As VMs de equilíbrio usando o Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) para criar duas máquinas virtuais para serem usadas como servidores back-end para o balanceador de carga. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Desativar políticas de rede de serviços do Private Link na sub-rede 
O serviço Private Link requer um IP de qualquer sub-rede de sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede nesses IPs.  Por isso, temos que desativar as políticas de rede na sub-rede. Atualize a sub-rede para desativar as políticas de rede de serviço satiscos com [a atualização da rede vnet da rede az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado  
 
Crie um serviço de Link Privado usando a configuração IP frontend do Balancer de carga padrão com [criação de serviço de link privado de rede az](/cli/azure/network/private-link-service#az-network-private-link-service-create). Este exemplo cria um serviço de link privado chamado *myPLS* usando o Standard Load Balancer chamado *myLoadBalancer* no grupo de recursos chamado *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Uma vez criado, tome nota do ID de serviço de link privado. Você precisará disso mais tarde para solicitar conexão a este serviço.  
 
Nesta fase, seu serviço Private Link é criado com sucesso e está pronto para receber o tráfego. Observe que o exemplo acima é apenas para demonstrar a criação do serviço Private Link usando o Azure CLI.  Não configuramos os pools de backend do balanceador de carga ou qualquer aplicativo nos pools de backend para ouvir o tráfego. Se você quiser ver fluxos de tráfego de ponta a ponta, é fortemente aconselhado a configurar seu aplicativo atrás do Balanceador de Carga Padrão.  
 
Em seguida, demonstraremos como mapear esse serviço para um ponto final privado em diferentes redes virtuais usando o Azure CLI. Novamente, o exemplo se limita a criar o endpoint privado e conectar-se ao serviço Private Link criado acima usando o Azure CLI. Além disso, você pode criar máquinas virtuais na rede virtual para enviar/receber tráfego para o ponto final privado.        
 
## <a name="private-endpoints"></a>Pontos finais privados

### <a name="create-the-virtual-network"></a>Criar a rede virtual 
Crie uma rede virtual com [a rede az vnet create](/cli/azure/network/vnet#az-network-vnet-create). Este exemplo cria uma rede virtual chamada *myPEVNet* no grupo de recursos chamado *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Criar a sub-rede 
Crie uma sub-rede em rede virtual [com a rede az vnet criar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Este exemplo cria uma sub-rede chamada *mySubnet* na rede virtual chamada *myPEVnet* no grupo de recursos chamado *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Desativar políticas privadas de rede de ponto final na sub-rede 
O ponto final privado pode ser criado em qualquer sub-rede de sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede em pontos finais privados.  Por isso, temos que desativar as políticas de rede na sub-rede. Atualize a sub-rede para desativar políticas privadas de rede de ponto final com [a atualização da sub-rede vnet da rede az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Crie ponto final privado e conecte-se ao serviço de link privado 
Crie um ponto final privado para consumir o serviço Private Link criado acima em sua rede virtual:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Você pode obter o *id de recursos de conexão privada* com `az network private-link-service show` o serviço Private Link. A id será como:   
/assinaturas/subID/resourceGroups/resourcegroupname*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/privatelinkservicename**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Mostrar conexões de serviço Private Link 
 
Consulte solicitações de conexão em seu serviço De Link Privado usando [a rede az private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o serviço Azure Private Link](private-link-service-overview.md)
 
