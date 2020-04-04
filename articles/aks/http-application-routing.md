---
title: Complemento de roteamento de aplicativos HTTP no AKS (Serviço de Kubernetes do Azure)
description: Use o complemento de roteamento de aplicativos HTTP para acessar aplicativos implantados no Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: 6ffc9daaf1b87fc9fb6ebbb0f2787f07282afe5e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632397"
---
# <a name="http-application-routing"></a>Roteamento de aplicativo HTTP

A solução de roteamento de aplicativos HTTP facilita o acesso a aplicativos implantados no cluster do AKS (Serviço de Kubernetes do Azure). Quando a solução está ativada, ela configura um [controlador Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) no seu cluster AKS. À medida que os aplicativos são implantados, a solução também cria nomes DNS publicamente acessíveis para os terminais de aplicativos.

Quando o complemento é habilitado, ele cria uma zona DNS na assinatura. Para obter mais informações sobre o custo DNS, consulte [preços do DNS][dns-pricing].

> [!CAUTION]
> O complemento de roteamento de aplicativos HTTP foi projetado para permitir que você crie rapidamente um controlador de entrada e acesse seus aplicativos. Esse complemento não é recomendado para uso em produção. Para implementações de ingresso prontos para produção que incluam várias réplicas e suporte a TLS, consulte [Criar um controlador de ingresso HTTPS](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>Visão geral da solução roteamento HTTP

O complemento implanta dois componentes: um [controlador Kubernetes Ingress][ingress] e um [controlador DNS externo.][external-dns]

- **Controlador de entrada**: o controlador de Entrada é exposto à internet usando um serviço de Kubernetes do tipo LoadBalancer. O controlador Ingress observa e implementa [os recursos do Kubernetes Ingress][ingress-resource], que cria rotas para os pontos finais dos aplicativos.
- **Controlador de DNS externo**: inspeciona os recursos de Entrada do Kubernetes e cria registros DNS A na zona DNS específica do cluster.

## <a name="deploy-http-routing-cli"></a>Implantar o roteamento HTTP: CLI

O complemento de roteamento de aplicativos HTTP pode ser habilitado com a CLI do Azure ao implantar um cluster do AKS. Para fazer isso, use o comando [az aks create][az-aks-create] com o argumento `--enable-addons`.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Se você quiser ativar vários complementos, forneça-os em forma de uma lista separada por vírgulas. Por exemplo, para habilitar o roteamento e monitoramento de aplicativos HTTP, use o formato `--enable-addons http_application_routing,monitoring`.

Você também pode ativar o roteamento HTTP em um cluster AKS existente usando o comando [az aks enable-addons][az-aks-enable-addons]. Para habilitar o roteamento de HTTP em um cluster existente, adicione a `--addons` parâmetro e especifique *http_application_routing* conforme mostrado no exemplo a seguir:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Depois que o cluster for implementado ou atualizado, use o comando [az aks show][az-aks-show] para recuperar o nome da zona DNS. Esse nome é necessário para implantar aplicativos no cluster do AKS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Result

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>Implantar roteamento HTTP: portal

O complemento de roteamento de aplicativos HTTP pode ser habilitado por meio do portal do Azure ao implantar um cluster do AKS.

![Habilitar o recurso de roteamento de HTTP](media/http-routing/create.png)

Depois que o cluster for implantado, navegue até o grupo de recursos do AKS criado automaticamente e selecione a zona DNS. Anote o nome da zona DNS. Esse nome é necessário para implantar aplicativos no cluster do AKS.

![Obter o nome da zona DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Usar o roteamento de HTTP

A solução de roteamento de aplicativos HTTP somente pode ser disparada nos recursos de Entrada que são anotados da seguinte forma:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Crie um arquivo nomeado **samples-http-application-routing.yaml** e copie no YAML a seguir. Na linha 43, atualize `<CLUSTER_SPECIFIC_DNS_ZONE>` com o nome da zona DNS coletada na etapa anterior deste artigo.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Use o comando [kubectl apply][kubectl-apply] para criar o segredo.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Use cURL ou um navegador para navegar até o nome do host especificado na seção do host do arquivo samples-http-application-routing.yaml. O aplicativo pode demorar até um minuto antes de ser disponibilizado pela Internet.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Remover o roteamento de HTTP

A solução de roteamento HTTP pode ser removida usando a CLI do Azure. Para fazer isso, execute o seguinte comando, substituindo seu cluster do AKS e o nome do grupo de recursos.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando o complemento de roteamento de aplicativo HTTP está desativado, alguns recursos do Kubernetes podem permanecer no cluster. Esses recursos incluem *configMaps* e *segredos*e são criados no namespace *kube-system*. Para manter um cluster limpo, você talvez queira remover esses recursos.

Procure recursos *addon-http-application-routing* usando os seguintes comandos [kubectl get][kubectl-get]:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A saída de exemplo a seguir mostra os configMaps que devem ser excluídos:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Para excluir recursos, use o comando [kubectl delete][kubectl-delete]. Especifique o tipo de recurso, o nome do recurso e o namespace. O exemplo a seguir exclui um dos configmaps anteriores:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Repita a etapa `kubectl delete` anterior para todos os recursos *addon-http-application-routing* que permaneceram no cluster.

## <a name="troubleshoot"></a>Solucionar problemas

Use o comando [kubectl logs][kubectl-logs] para exibir os logs de aplicativo para o aplicativo de DNS externo. Os logs devem confirmar que um registro DNS A e TXT foi criado com êxito.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Esses registros também podem ser vistos no recurso de zona de DNS no portal do Azure.

![Obter os registros DNS](media/http-routing/clippy.png)

Use o comando [kubectl logs][kubectl-logs] para visualizar os registros de aplicativos do controlador Nginx Ingress. Os logs devem confirmar o `CREATE` de um recurso de Entrada e o recarregamento do controlador. Toda a atividade de HTTP é registrada.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Limpar

Remova os objetos do Kubernetes associados criados neste artigo.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como instalar um controlador de Entrada protegido por HTTPS no AKS, consulte [Entrada HTTPS no AKS (Serviço de Kubernetes do Azure)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
