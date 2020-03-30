---
title: Crie um cluster privado de serviços Azure Kubernetes
description: Saiba como criar um cluster aks (AKS) privado do Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499293"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Crie um cluster privado de serviços Azure Kubernetes

Em um cluster privado, o plano de controle ou servidor De PiPi possui endereços IP internos definidos no documento [RFC1918 - Alocação de endereços para Internets privadas.](https://tools.ietf.org/html/rfc1918) Ao usar um cluster privado, você pode garantir que o tráfego de rede entre seu servidor de API e seus pools de nós permaneça apenas na rede privada.

O plano de controle ou servidor API está em uma assinatura Azure Kubernetes Service (AKS) gerenciada pelo Azure. O cluster ou pool de nó de um cliente está na assinatura do cliente. O servidor e o pool de cluster ou nó podem se comunicar entre si através do [serviço Azure Private Link][private-link-service] na rede virtual do servidor API e de um ponto final privado exposto na sub-rede do cluster AKS do cliente.

## <a name="prerequisites"></a>Pré-requisitos

* A versão 2.2.0 ou posterior do Azure CLI

## <a name="create-a-private-aks-cluster"></a>Crie um cluster AKS privado

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ou use um grupo de recursos existente para o cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde *--habilitar-private-cluster* é uma bandeira obrigatória para um cluster privado. 

### <a name="advanced-networking"></a>Rede avançada  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Onde *--habilitar-private-cluster* é uma bandeira obrigatória para um cluster privado. 

> [!NOTE]
> Se a ponte Docker abordar CIDR (172.17.0.1/16) estiver em conflito com a sub-rede CIDR, altere o endereço da ponte Docker apropriadamente.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opções para se conectar ao cluster privado

O ponto final do servidor api não tem endereço IP público. Para gerenciar o servidor de API, você precisará usar uma VM que tenha acesso à Rede Virtual Azure (VNet) do cluster AKS. Existem várias opções para estabelecer conectividade de rede ao cluster privado.

* Crie uma VM na mesma Rede Virtual Azure (VNet) que o cluster AKS.
* Use uma VM em uma rede separada e configure [peering de rede virtual][virtual-network-peering].  Consulte a seção abaixo para obter mais informações sobre essa opção.
* Use uma [conexão Express Route ou VPN.][express-route-or-VPN]

Criar uma VM no mesmo VNET do cluster AKS é a opção mais fácil.  Express Route e VPNs adicionam custos e requerem complexidade adicional de rede.  O peering de rede virtual exige que você planeje as faixas de CIDR da rede para garantir que não haja faixas sobrepostas.

## <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

Como mencionado, o peering VNet é uma maneira de acessar seu cluster privado. Para usar o vnet peering, você precisa configurar um link entre a rede virtual e a zona dNS privada.
    
1. Acesse o grupo de recursos MC_* no portal Azure.  
2. Selecione a zona DNS privada.   
3. No painel esquerdo, selecione o link **de rede Virtual.**  
4. Crie um novo link para adicionar a rede virtual da VM à zona dns privada. Leva alguns minutos para que o link da zona DNS fique disponível.  
5. Volte para o grupo de recursos MC_* no portal Azure.  
6. No painel direito, selecione a rede virtual. O nome da rede virtual está no formulário *aks-vnet.\**  
7. No painel esquerdo, selecione **Peerings**.  
8. Selecione **Adicionar,** adicionar a rede virtual da VM e, em seguida, criar o peering.  
9. Vá para a rede virtual onde você tem a VM, selecione **Peerings,** selecione a rede virtual AKS e, em seguida, crie o peering. Se o endereço for deacordo com a rede virtual AKS e o confronto da rede virtual da VM, o peering falhará. Para obter mais informações, consulte [Peering de rede virtual][virtual-network-peering].

## <a name="dependencies"></a>Dependências  

* O serviço Private Link é suportado apenas no Standard Azure Load Balancer. O Balancer de Carga Básica Azure não é suportado.  
* Para usar um servidor DNS personalizado, adicione o IP 168.63.63.129.16 do Azure DNS como o servidor DNS upstream no servidor DNS personalizado.

## <a name="limitations"></a>Limitações 
* As faixas autorizadas por IP não podem ser aplicadas ao ponto final do servidor de api privado, elas só se aplicam ao servidor público de API
* As Zonas de Disponibilidade são atualmente suportadas para determinadas regiões, veja o início deste documento 
* [As limitações de serviço do Azure Private Link][private-link-service] se aplicam a clusters privados, pontos finais privados do Azure e pontos finais de serviço de rede virtual, que atualmente não são suportados na mesma rede virtual.
* Sem suporte para nódulos virtuais em um cluster privado para girar a ACI (Private Azure Container Instances, instâncias de contêiner do Azure) em uma rede virtual privada do Azure
* Sem suporte para integração do Azure DevOps fora da caixa com clusters privados
* Para os clientes que precisam habilitar o Registro de Contêineres do Azure para trabalhar com AKS privados, a rede virtual container Registry deve ser pesquisada com a rede virtual do cluster de agentes.
* Sem suporte atual para espaços azure dev
* Sem suporte para converter clusters AKS existentes em clusters privados
* Excluir ou modificar o ponto final privado na sub-rede do cliente fará com que o cluster pare de funcionar. 
* O Monitor Do Azul para contêineres O Live Data não é suportado no momento.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

