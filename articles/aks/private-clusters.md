---
title: Criar um cluster particular do serviço kubernetes do Azure
description: Saiba como criar um cluster do AKS (serviço de kubernetes do Azure) privado
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 0a05bd15fff97d4f0020f6ce82ee90a2fe995edf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944211"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Criar um cluster privado do serviço kubernetes do Azure (versão prévia)

Em um cluster privado, o plano de controle ou o servidor de API tem endereços IP internos que são definidos no documento [alocação de endereço RFC1918 para Internet privada](https://tools.ietf.org/html/rfc1918) . Usando um cluster privado, você pode garantir que o tráfego de rede entre o servidor de API e os pools de nós permaneça apenas na rede privada.

O plano de controle ou o servidor de API está em uma assinatura do Azure gerenciada pelo AKS (serviço de kubernetes do Azure). O cluster ou o pool de nós de um cliente está na assinatura do cliente. O servidor e o cluster ou pool de nós podem se comunicar entre si por meio do [serviço de vínculo privado do Azure][private-link-service] na rede virtual do servidor de API e um ponto de extremidade privado exposto na sub-rede do cluster AKs do cliente.

> [!IMPORTANT]
> Os recursos de visualização do AKS são de autoatendimento e são oferecidos de acordo com a aceitação. As visualizações são fornecidas *como estão* e disponíveis e são excluídas do SLA (contrato de nível de serviço) e *da* garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente em uma base de *melhor esforço* . Portanto, os recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS](support-policies.md)
> * [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="prerequisites"></a>Prerequisites

* O CLI do Azure versão 2.0.77 ou posterior e a versão de extensão de visualização CLI do Azure AKS 0.4.18

## <a name="currently-supported-regions"></a>Regiões com suporte no momento

* Leste da Austrália
* Sudeste da Austrália
* Sul do Brasil
* Canadá Central
* Leste do Canadá
* Cenral nós
* Leste da Ásia
* Leste dos EUA
* Leste dos EUA 2
* Leste dos EUA 2 EUAP
* França Central
* Norte da Alemanha
* Leste do Japão
* Oeste do Japão
* Coreia Central
* Sul da Coreia
* Centro-Norte dos EUA
* Norte da Europa
* Norte da Europa
* Centro-Sul dos Estados Unidos
* Sul do Reino Unido
* Europa Ocidental
* Oeste dos EUA
* Oeste dos EUA 2
* Leste dos EUA 2

## <a name="currently-supported-availability-zones"></a>Zonas de Disponibilidade atualmente com suporte

* Centro dos EUA
* Leste dos EUA
* Leste dos EUA 2
* França Central
* Leste do Japão
* Norte da Europa
* Sudeste Asiático
* Sul do Reino Unido
* Europa Ocidental
* Oeste dos EUA 2

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instalar a extensão mais recente do CLI do Azure AKS Preview

Para usar clusters privados, você precisa do CLI do Azure extensão de visualização AKS versão 0.4.18 ou posterior. Instale a extensão de visualização do CLI do Azure AKS usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o seguinte comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, você pode usar as configurações padrão para todos os clusters AKS que foram criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status do registro seja exibido como *registrado*. Você pode verificar o status usando o seguinte comando de [lista de recursos do AZ][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Quando o estado for registrado, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o seguinte comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Criar um cluster AKS privado

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ou use um grupo de recursos existente para o cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde *--Enable-Private-cluster* é um sinalizador obrigatório para um cluster privado. 

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
Onde *--Enable-Private-cluster* é um sinalizador obrigatório para um cluster privado. 

> [!NOTE]
> Se o endereço de ponte do Docker (172.17.0.1/16) conflitar com o CIDR da sub-rede, altere o endereço da ponte do Docker adequadamente.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opções para se conectar ao cluster privado

O ponto de extremidade do servidor de API não tem nenhum endereço IP público. Para gerenciar o servidor de API, será necessário usar uma VM que tenha acesso à VNet (rede virtual) do Azure do cluster AKS. Há várias opções para estabelecer a conectividade de rede com o cluster privado.

* Crie uma VM na mesma VNet (rede virtual) do Azure que o cluster AKS.
* Use uma VM em uma rede separada e configure o [emparelhamento de rede virtual][virtual-network-peering].  Consulte a seção abaixo para obter mais informações sobre essa opção.
* Use uma conexão [VPN ou de rota expressa][express-route-or-VPN] .

Criar uma VM na mesma VNET que o cluster AKS é a opção mais fácil.  A rota expressa e as VPNs adicionam custos e exigem complexidade adicional de rede.  O emparelhamento de rede virtual exige que você planeje os intervalos de CIDR de rede para garantir que não haja intervalos sobrepostos.

## <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

Conforme mencionado, o emparelhamento VNet é uma maneira de acessar seu cluster privado. Para usar o emparelhamento VNet, você precisa configurar um vínculo entre a rede virtual e a zona DNS privada.
    
1. Vá para o MC_ * grupo de recursos no portal do Azure.  
2. Selecione a zona DNS privada.   
3. No painel esquerdo, selecione o link **rede virtual** .  
4. Crie um novo link para adicionar a rede virtual da VM à zona DNS privada. Leva alguns minutos para que o link da zona DNS fique disponível.  
5. Volte para o grupo de recursos MC_ * no portal do Azure.  
6. No painel direito, selecione a rede virtual. O nome da rede virtual está no formato *AKs-vnet-\** .  
7. No painel esquerdo, selecione **emparelhamentos**.  
8. Selecione **Adicionar**, adicione a rede virtual da VM e, em seguida, crie o emparelhamento.  
9. Vá para a rede virtual onde você tem a VM, selecione **emparelhamentos**, selecione a rede virtual AKs e, em seguida, crie o emparelhamento. Se os intervalos de endereços na rede virtual AKS e o conflito de rede virtual da VM, o emparelhamento falhará. Para obter mais informações, consulte [emparelhamento de rede virtual][virtual-network-peering].

## <a name="dependencies"></a>Dependências  
* O serviço de vínculo privado tem suporte somente no Azure Load Balancer padrão. Não há suporte para Azure Load Balancer básica.  
* Para usar um servidor DNS personalizado, implante um servidor do AD com o DNS para encaminhar para este IP 168.63.129.16

## <a name="limitations"></a>Limitações 
* Os intervalos autorizados por IP não podem ser aplicados ao ponto de extremidade do servidor de API privada, eles se aplicam somente ao servidor de API pública
* Atualmente, há suporte para Zonas de Disponibilidade em determinadas regiões, consulte o início deste documento 
* As [limitações do serviço de vínculo privado do Azure][private-link-service] se aplicam a clusters privados, pontos de extremidade privados do Azure e pontos de extremidade de serviço de rede virtual, que atualmente não têm suporte na mesma rede virtual.
* Não há suporte para nós virtuais em um cluster privado para girar ACI (instâncias de contêiner do Azure particulares) em uma rede virtual do Azure privada
* Não há suporte para a integração do Azure DevOps pronta para uso com clusters privados
* Para clientes que precisam habilitar o registro de contêiner do Azure para trabalhar com AKS particulares, a rede virtual do registro de contêiner deve ser emparelhada com a rede virtual do cluster do agente.
* Não há suporte atual para Azure Dev Spaces
* Não há suporte para converter clusters AKS existentes em clusters privados
* Excluir ou modificar o ponto de extremidade privado na sub-rede do cliente fará com que o cluster pare de funcionar. 
* No momento, não há suporte para Azure Monitor para contêineres de dados dinâmicos.
* Não há suporte para *traga seu próprio DNS* no momento.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

