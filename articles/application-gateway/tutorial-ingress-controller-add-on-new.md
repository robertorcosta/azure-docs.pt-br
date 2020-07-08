---
title: Habilitar o complemento do controlador de entrada para um novo cluster AKS com uma nova instância de gateway de Aplicativo Azure
description: Saiba como usar o CLI do Azure para habilitar o complemento do controlador de entrada para o novo cluster AKS com uma nova instância do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 439313f0f42adf0513ce490ab6569171cce7934b
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037895"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Tutorial: habilitar o complemento do controlador de entrada (versão prévia) para um novo cluster AKS com uma nova instância do gateway de aplicativo

Você pode usar o CLI do Azure para habilitar o complemento [AGIC (controlador de entrada do gateway de aplicativo)](ingress-controller-overview.md) para um cluster [do AKS (serviços Kubernetess do Azure)](https://azure.microsoft.com/services/kubernetes-service/) . O complemento está atualmente em visualização.

Neste tutorial, você criará um cluster AKS com o complemento AGIC habilitado. A criação do cluster criará automaticamente uma instância de gateway de Aplicativo Azure a ser usada. Em seguida, você implantará um aplicativo de exemplo que usará o complemento para expor o aplicativo por meio do gateway de aplicativo. 

O complemento fornece uma maneira muito mais rápida de implantar o AGIC para o cluster AKS do que [antes por meio do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Ele também oferece uma experiência totalmente gerenciada.    

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um grupos de recursos. 
> * Crie um novo cluster AKS com o complemento AGIC habilitado. 
> * Implante um aplicativo de exemplo usando o AGIC para entrada no cluster AKS.
> * Verifique se o aplicativo pode ser acessado por meio do gateway de aplicativo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Registre o sinalizador de recurso *AKs-IngressApplicationGatewayAddon* usando o comando [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , conforme mostrado no exemplo a seguir. Você precisará fazer isso apenas uma vez por assinatura, enquanto o complemento ainda está em versão prévia.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Pode levar alguns minutos para que o status seja exibido `Registered` . Você pode verificar o status de registro usando o comando [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos Microsoft. ContainerService usando o comando [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Instale ou atualize a extensão AKs-Preview para este tutorial. Use os seguintes comandos de CLI do Azure:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *canadacentral* (Region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Implantar um cluster AKS com o complemento habilitado

Agora, você implantará um novo cluster AKS com o complemento AGIC habilitado. Se você não fornecer uma instância existente do gateway de aplicativo para usar nesse processo, criaremos e configuraremos automaticamente uma nova instância do gateway de aplicativo para servir o tráfego para o cluster AKS.  

> [!NOTE]
> O complemento do controlador de entrada do gateway de aplicativo *só* dá suporte a SKUs do gateway de aplicativo v2 (Standard e WAF) e *não* às SKUs do Application Gateway v1. Quando você estiver implantando uma nova instância do gateway de aplicativo por meio do complemento AGIC, poderá implantar apenas um gateway de aplicativo Standard_v2 SKU. Se você quiser habilitar o complemento para um gateway de aplicativo WAF_v2 SKU, use um destes métodos:
>
> - Habilite o WAF no gateway de aplicativo por meio do Portal. 
> - Crie a instância WAF_v2 gateway de aplicativo primeiro e siga as instruções sobre como [habilitar o complemento AGIC com um cluster existente do AKs e uma instância existente do gateway de aplicativo](tutorial-ingress-controller-add-on-existing.md). 

No exemplo a seguir, você implantará um novo cluster AKS chamado *mycluster* usando o [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e [identidades gerenciadas](https://docs.microsoft.com/azure/aks/use-managed-identity). O complemento AGIC será habilitado no grupo de recursos que você criou, *MyResource*Group. 

Implantar um novo cluster AKS com o complemento AGIC habilitado sem especificar uma instância de gateway de aplicativo existente significará uma criação automática de uma instância de gateway de aplicativo Standard_v2 SKU. Portanto, você também especificará o nome e o espaço de endereço de sub-rede da instância do gateway de aplicativo. O nome da instância do gateway de aplicativo será *myApplicationGateway*e o espaço de endereço de sub-rede que estamos usando será 10.2.0.0/16. Verifique se você adicionou ou atualizou a extensão AKs-Preview no início deste tutorial. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Para configurar parâmetros adicionais para o `az aks create` comando, consulte [essas referências](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> O cluster AKS que você criou aparecerá no grupo de recursos que você criou, *MyResource*Group. No entanto, a instância do gateway de aplicativo criada automaticamente estará no grupo de recursos do nó, onde os pools de agentes são. O grupo de recursos do nó por é chamado de *MC_resource-Group-name_cluster-name_location* por padrão, mas pode ser modificado. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Implantar um aplicativo de exemplo usando o AGIC

Agora, você implantará um aplicativo de exemplo no cluster AKS que você criou. O aplicativo usará o complemento AGIC para entrada e conectará a instância do gateway de aplicativo ao cluster AKS. 

Primeiro, obtenha as credenciais para o cluster AKS executando o `az aks get-credentials` comando: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Agora que você tem credenciais, execute o comando a seguir para configurar um aplicativo de exemplo que usa AGIC para entrada no cluster. O AGIC atualizará a instância do gateway de aplicativo que você configurou anteriormente com as regras de roteamento correspondentes para o novo aplicativo de exemplo que você implantou.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificar se o aplicativo está acessível

Agora que a instância do gateway de aplicativo está configurada para fornecer tráfego para o cluster AKS, vamos verificar se seu aplicativo está acessível. Primeiro, obtenha o endereço IP da entrada: 

```azurecli-interactive
kubectl get ingress
```

Verifique se o aplicativo de exemplo que você criou está sendo executado por um dos dois:

- Visitando o endereço IP da instância do gateway de aplicativo que você obteve ao executar o comando anterior.
- Usando o `curl` . 

O gateway de aplicativo pode levar um minuto para obter a atualização. Se o gateway de aplicativo ainda estiver em um estado de **atualização** no portal, deixe que ele seja concluído antes de tentar acessar o endereço IP. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deles, remova o grupo de recursos, a instância do gateway de aplicativo e todos os recursos relacionados:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre como desabilitar o complemento AGIC](./ingress-controller-disable-addon.md)
* [Saiba quais anotações têm suporte com o AGIC](./ingress-controller-annotations.md)
* [Solucionar problemas com o AGIC](./ingress-controller-troubleshoot.md)

