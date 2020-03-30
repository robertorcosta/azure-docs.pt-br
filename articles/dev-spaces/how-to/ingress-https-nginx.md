---
title: Use um controlador de ingestão NGINX personalizado e configure HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Aprenda a configurar o Azure Dev Spaces para usar um controlador de ingestão NGINX personalizado e configurar HTTPS usando esse controlador de ingestão
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 0fe9fec263b72ac06839b58fdc5b0142a724718c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155440"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>Use um controlador de ingestão NGINX personalizado e configure HTTPS

Este artigo mostra como configurar o Azure Dev Spaces para usar um controlador de ingestão NGINX personalizado. Este artigo também mostra como configurar esse controlador de ingestão personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, poderá [criar uma conta gratuita][azure-account-create].
* A [CLI do Azure][az-cli] instalada.
* [Cluster Azure Kubernetes Service (AKS) com Azure Dev Spaces ativado][qs-cli].
* [kubectl][kubectl] instalado.
* [Helm 3 instalado][helm-installed].
* [Um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone].  Este artigo pressupõe que o domínio personalizado e a Zona DNS estão no mesmo grupo de recursos do seu cluster AKS, mas é possível usar um domínio personalizado e uma Zona DNS em um grupo de recursos diferente.

## <a name="configure-a-custom-nginx-ingress-controller"></a>Configure um controlador de ingestão NGINX personalizado

Conecte-se ao cluster usando [kubectl][kubectl], o cliente da linha de comando Kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adicione o [repositório oficial estável Helm,][helm-stable-repo]que contém o gráfico helm do controlador de ingestão NGINX.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Crie um namespace kubernetes para o controlador de ingestão `helm`NGINX e instale-o usando .

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> O exemplo acima cria um ponto final público para o seu controlador de ingress. Se você precisar usar um ponto final privado para o controlador de entrada, adicione as *anotações --set controller.service.anotações." serviço\\.beta\\\\.kubernetes .io/azure-load-balancer-internal"=parâmetro verdadeiro* para o comando helm *install.* Por exemplo: 
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> Este ponto final privado é exposto dentro da rede virtual onde o cluster AKS é implantado.

Obtenha o endereço IP do serviço de controlador de ingress nginx usando [kubectl get][kubectl-get].

```console
kubectl get svc -n nginx --watch
```

A saída de amostra mostra os endereços IP de todos os serviços no espaço de nome *nginx.*

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

Adicione um registro *A* à sua região De DNS com o endereço IP externo do serviço NGINX usando [a rede az dns gravar um add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um registro *A* à *MY_CUSTOM_DOMAIN* zona DNS.

Neste artigo, você usa o [Aplicativo de exemplo de Compartilhamento de Bicicletas do Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar o uso do Azure Dev Spaces. Clone o aplicativo do GitHub e navegue para seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [valores.yaml][values-yaml] e faça as seguintes atualizações:
* Substitua todas as instâncias de *<REPLACE_ME_WITH_HOST_SUFFIX>* por *nginx. MY_CUSTOM_DOMAIN* usando seu domínio para *MY_CUSTOM_DOMAIN*. 
* Substitua *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* com *kubernetes.io/ingress.class: nginx # Custom Ingress*. 

Abaixo está um exemplo `values.yaml` de um arquivo atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Salve suas alterações e feche o arquivo.

Crie o espaço *de desenvolvimento* `azds space select`com sua aplicação de amostra usando .

```console
azds space select -n dev -y
```

Implantar o aplicativo `helm install`de amostra usando .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

O exemplo acima implanta o aplicativo de exemplo no espaço de nome *de desenvolvimento.*

Exibir os URLs para acessar `azds list-uris`o aplicativo de amostra usando .

```console
azds list-uris
```

A saída abaixo mostra o `azds list-uris`exemplo urls de .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública com o comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Se você vir uma página de erro em vez do serviço web de compartilhamento de *bicicletas,* verifique se atualizou **tanto** a anotação *kubernetes.io/ingress.class* quanto o host no arquivo *values.yaml.*

Use `azds space select` o comando para criar um espaço infantil *o desenvolvimento* e liste os URLs para acessar o espaço de desenvolvimento infantil.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra o `azds list-uris` exemplo de URLs para acessar o aplicativo de amostra no espaço de desenvolvimento infantil *azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *de bikesharing web* no espaço de desenvolvimento infantil `azds list-uris` *azureuser1* abrindo a URL pública a partir do comando. No exemplo acima, a URL pública para o serviço web de compartilhamento de `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` *bicicletas* no espaço de desenvolvimento infantil *azureuser1* é .

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>Configure o controlador de ingestão NGINX para usar HTTPS

Use [o cert-manager][cert-manager] para automatizar o gerenciamento do certificado TLS ao configurar seu controlador de entrada NGINX para usar HTTPS. Use `helm` para instalar o *gráfico certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Crie `letsencrypt-clusterissuer.yaml` um arquivo e atualize o campo de e-mail com seu endereço de e-mail.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: nginx
```

> [!NOTE]
> Para testes, há também um [servidor de preparação][letsencrypt-staging-issuer] que você pode usar para o *clusterIssuer*.

Use `kubectl` para `letsencrypt-clusterissuer.yaml`aplicar .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

Atualize [os valores.yaml][values-yaml] para incluir os detalhes para o uso *do cert-manager* e https. Abaixo está um exemplo `values.yaml` de um arquivo atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Atualize o `helm`aplicativo de amostra usando:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navegue até o aplicativo de exemplo no espaço *infantil dev/azureuser1* e observe que você é redirecionado para usar HTTPS. Observe também que a página é carregada, mas o navegador mostra alguns erros. A abertura do console do navegador mostra que o erro está relacionado a uma página HTTPS que tenta carregar recursos HTTP. Por exemplo: 

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir esse erro, [atualize o BikeSharingWeb/azds.yaml][azds-yaml] semelhante ao abaixo:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

Atualize [o BikeSharingWeb/package.json][package-json] com uma dependência para o pacote *de url.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Atualize o método *getApiHostAsync* no [BikeSharingWeb/lib/helpers.js][helpers-js] para usar HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navegue `BikeSharingWeb` até o `azds up` diretório e use para executar seu serviço *atualizado do BikeSharingWeb.*

```console
cd ../BikeSharingWeb/
azds up
```

Navegue até o aplicativo de exemplo no espaço *infantil dev/azureuser1* e observe que você é redirecionado para usar HTTPS sem erros.

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml