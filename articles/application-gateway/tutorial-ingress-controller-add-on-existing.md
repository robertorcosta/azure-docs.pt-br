---
title: Habilitar o complemento do controlador de entrada para o cluster AKS existente com o gateway de Aplicativo Azure existente
description: Use este tutorial para habilitar o complemento do controlador de entrada para o cluster AKS existente com um gateway de aplicativo existente
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 42952e379b9f68008de23ee3b1717280d8dd6cb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088097"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Tutorial: habilitar o complemento do controlador de entrada do gateway de aplicativo para um cluster AKS existente com um gateway de aplicativo existente por meio de CLI do Azure (versão prévia)

Você pode usar CLI do Azure para habilitar o complemento [AGIC (controlador de entrada do gateway de aplicativo)](ingress-controller-overview.md) , que está atualmente em versão prévia, para seu cluster do [AKs (serviços Kubernetess do Azure)](https://azure.microsoft.com/services/kubernetes-service/) . Neste tutorial, você aprenderá a usar o complemento AGIC para expor seu aplicativo kubernetes em um cluster AKS existente por meio de um gateway de aplicativo existente implantado em redes virtuais separadas. Você começará criando um cluster AKS em uma rede virtual e um gateway de aplicativo em uma rede virtual separada para simular os recursos existentes. Em seguida, você habilitará o complemento AGIC, emparelhará as duas redes virtuais e implantará um aplicativo de exemplo que será exposto por meio do gateway de aplicativo usando o complemento AGIC. Se você estiver habilitando o complemento AGIC para um gateway de aplicativo existente e um cluster AKS existente na mesma rede virtual, poderá ignorar a etapa de emparelhamento abaixo. O complemento fornece uma maneira muito mais rápida de implantar o AGIC para o cluster AKS do que [antes por meio do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e também oferece uma experiência totalmente gerenciada.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de recursos 
> * Criar um novo cluster AKS 
> * Criar um novo gateway de aplicativo 
> * Habilitar o complemento AGIC no cluster AKS existente usando o gateway de aplicativo existente 
> * Emparelhar a rede virtual do gateway de aplicativo com a rede virtual de cluster AKS
> * Implantar um aplicativo de exemplo usando o AGIC para entrada no cluster AKS
> * Verifique se o aplicativo pode ser acessado por meio do gateway de aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Registre o sinalizador de recurso *AKs-IngressApplicationGatewayAddon* usando o comando [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , conforme mostrado no exemplo a seguir; Você só precisará fazer isso uma vez por assinatura, enquanto o complemento ainda está em versão prévia:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Pode levar alguns minutos para que o status seja mostrado registrado. Você pode verificar o status de registro usando o comando [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos Microsoft.ContainerService usando o comando [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register):
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Certifique-se de instalar/atualizar a extensão AKs-Preview para este tutorial; Use os seguintes comandos de CLI do Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *canadacentral* (região). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Implantar um novo cluster AKS

Agora você implantará um novo cluster AKS, para simular ter um cluster AKS existente para o qual você deseja habilitar o complemento AGIC.  

No exemplo a seguir, você implantará um novo cluster AKS chamado *mycluster* usando o [CNI do Azure](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e [identidades gerenciadas](https://docs.microsoft.com/azure/aks/use-managed-identity) no grupo de recursos que você criou, *MyResource*Group.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Para configurar parâmetros adicionais para o `az aks create` comando, visite referências [aqui](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Implantar um novo gateway de aplicativo 

Agora você implantará um novo gateway de aplicativo, para simular ter um gateway de aplicativo existente que você deseja usar para balancear a carga do tráfego para o cluster AKS, *mycluster*. O nome do gateway de aplicativo será *myApplicationGateway*, mas você precisará primeiro criar um recurso de IP público, chamado *myPublicIp*, e uma nova rede virtual chamada *myVnet* com o espaço de endereço 11.0.0.0/8, e uma sub-rede com espaço de endereço 11.1.0.0/16 chamada *mysubnet*e implantar o gateway de aplicativo em *mysubnet* usando *myPublicIp*. 

Ao usar um cluster AKS e um gateway de aplicativo em redes virtuais separadas, os espaços de endereço das duas redes virtuais não devem se sobrepor. O espaço de endereço padrão em que um cluster AKS é implantado é 10.0.0.0/8, portanto, definimos o prefixo de endereço de rede virtual do gateway de aplicativo como 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> O complemento AGIC (controlador de entrada do gateway de aplicativo) **só** dá suporte a SKUs do gateway de aplicativo v2 (Standard e WAF) e **não** aos SKUs do Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Habilitar o complemento AGIC no cluster AKS existente com o gateway de aplicativo existente 

Agora, você habilitará o complemento AGIC no cluster AKS que você criou, *mycluster*e especificará o complemento AGIC para usar o gateway de aplicativo existente que você criou, *myApplicationGateway*. Verifique se você adicionou/atualizou a extensão AKs-Preview no início deste tutorial. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Emparelhar as duas redes virtuais

Como implantamos o cluster AKS em sua própria rede virtual e o gateway de aplicativo em outra rede virtual, você precisará emparelhar as duas redes virtuais juntas para que o tráfego flua do gateway de aplicativo para o pods no cluster. O emparelhamento das duas redes virtuais requer a execução do CLI do Azure comando duas vezes diferentes, para garantir que a conexão seja bidirecional. O primeiro comando criará uma conexão de emparelhamento da rede virtual do gateway de aplicativo para a rede virtual AKS; o segundo comando criará uma conexão de emparelhamento na outra direção. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Implantar um aplicativo de exemplo usando o AGIC 

Agora, você implantará um aplicativo de exemplo no cluster AKS que você criou, que usará o complemento AGIC para entrada e conectará o gateway de aplicativo ao cluster AKS. Primeiro, você receberá credenciais para o cluster AKS implantado executando o `az aks get-credentials` comando. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Depois de ter as credenciais para o cluster que você criou, execute o comando a seguir para configurar um aplicativo de exemplo que usa AGIC para entrada no cluster. O AGIC atualizará o gateway de aplicativo que você configurou anteriormente com as regras de roteamento correspondentes para o novo aplicativo de exemplo implantado.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificar se o aplicativo está acessível

Agora que o gateway de aplicativo está configurado para fornecer tráfego para o cluster AKS, vamos verificar se seu aplicativo está acessível. Primeiro, você obterá o endereço IP da entrada. 

```azurecli-interactive
kubectl get ingress
```

Verifique se o aplicativo de exemplo que você criou está em funcionamento visitando o endereço IP do gateway de aplicativo que você obteve ao executar o comando acima ou fazer check-in `curl` . Pode levar um minuto ao gateway de aplicativo para obter a atualização, portanto, se o gateway de aplicativo ainda estiver em um estado de "atualização" no portal, deixe que ele seja concluído antes de tentar acessar o endereço IP. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre como desabilitar o complemento AGIC](./ingress-controller-disable-addon.md)
* [Saiba mais sobre quais anotações têm suporte com o AGIC](./ingress-controller-annotations.md)
* [Solucionar problemas com o AGIC](./ingress-controller-troubleshoot.md)

