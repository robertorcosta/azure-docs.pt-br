---
title: Use um endereço IP estático e um rótulo DNS com o balanceador de carga Azure Kubernetes Service (AKS)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047961"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Use um endereço IP público estático e um rótulo DNS com o balanceador de carga Azure Kubernetes Service (AKS)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster do AKS só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

Este artigo abrange o uso de um *IP Padrão* SKU com um balanceador de carga *SKU* padrão. Para obter mais informações, consulte [os tipos de endereço IP e os métodos de alocação no Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Crie um endereço IP público estático com o comando [ip create público da rede az.][az-network-public-ip-create] A seguir, cria um recurso IP estático chamado *myAKSPublicIP* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se você estiver usando um *balanceador de* carga Basic SKU no cluster AKS, use *Basic* para o parâmetro *sku* ao definir um IP público. Apenas os IPs *SKU básicos* funcionam com o balanceador de carga *Basic* SKU e apenas os IPs *SKU padrão* funcionam com balanceadores de carga *SKU padrão.* 

O endereço IP é exibido, conforme mostrado na seguinte saída de exemplo condensado:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Mais tarde, você poderá obter o endereço IP público usando o comando [az network public-ip list][az-network-public-ip-list]. Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e, em seguida, confira o *ipAddress*, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Antes de criar um serviço, certifique-se de que o principal de serviço usado pelo cluster AKS tenha delegado permissões para o outro grupo de recursos. Por exemplo: 

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Alternativamente, você pode usar o sistema atribuído identidade gerenciada para permissões em vez do diretor de serviço. Para obter mais informações, consulte [Usar identidades gerenciadas](use-managed-identity.md).

Para criar um serviço *LoadBalancer* com o `loadBalancerIP` endereço IP público estático, adicione a propriedade e o valor do endereço IP público estático ao manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior. O exemplo a seguir também define a anotação para o grupo de recursos chamado *myResourceGroup*. Forneça seu próprio nome de grupo de recursos.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Aplique um rótulo DNS ao serviço

Se o seu serviço estiver usando um endereço IP público dinâmico `service.beta.kubernetes.io/azure-dns-label-name` ou estático, você pode usar a anotação do serviço para definir um rótulo DNS voltado para o público. Isso publica um nome de domínio totalmente qualificado para o seu serviço usando os servidores DNS públicos do Azure e o domínio de alto nível. O valor da anotação deve ser único dentro da localização do Azure, por isso recomenda-se usar um rótulo suficientemente qualificado.   

O Azure então anexará automaticamente uma sub-rede padrão, como `<location>.cloudapp.azure.com` (onde a localização é a região selecionada), ao nome que você fornece, para criar o nome DNS totalmente qualificado. Por exemplo: 

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Para publicar o serviço em seu próprio domínio, consulte [o Azure DNS][azure-dns-zone] e o projeto [external-dns.][external-dns]

## <a name="troubleshoot"></a>Solução de problemas

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto de serviço Kubernetes não existir ou não tiver sido criado no grupo de recursos do nó e nenhuma delegação adicional configurada, a criação do serviço balanceador de carga falhará. Para solucionar problemas, examine os eventos de criação do serviço com o comando [kubectl describe][kubectl-describe]. Forneça o nome do serviço, conforme especificado no manifesto do YAML, como é mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

As informações sobre o recurso do serviço de Kubernetes são exibidas. Os *Eventos* no final da saída de exemplo a seguir indicam que o *endereço IP fornecido pelo usuário não foi encontrado*. Nesses cenários, verifique se você criou o endereço IP público estático no grupo de recursos do nó e se o endereço IP especificado no manifesto do serviço de Kubernetes está correto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais controle sobre o tráfego de rede para seus aplicativos, convém [criar um controlador de entrada][aks-ingress-basic]. Você também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
