---
title: Crie um controlador de entrada HTTP com um endereço IP estático no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e configurar um controlador de entrada NGINX com um endereço IP público estático em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 3e79bbe76a751097acd5c9d3c42dbd4020b6866b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617282"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Crie um controlador de entrada com um endereço IP público estático no AKS (Serviço de Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) O controlador de entrada é configurado com um endereço IP público estático. O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente certificados [Vamos Criptografar][lets-encrypt]. Finalmente, dois aplicativos são executados no cluster do AKS, cada um dos quais é acessível em um único endereço IP.

Também é possível:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de ingresso que usa o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público dinâmico][aks-ingress-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Este artigo usa o Helm para instalar o NGINX ingress controller, Gerenciador de certificado e um aplicativo web de exemplo. Verifique se você está usando a versão mais recente do Helm. Para obter instruções de atualização, consulte os [docs de instalação do Helm][helm-install]. Para obter mais informações sobre a configuração e o uso do Helm, consulte [Instalar aplicativos com o Helm no Azure Kubernetes Service (AKS)][use-helm].

Este artigo também exige que você esteja executando a versão 2.0.64 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por padrão, um controlador de entrada é criado com uma nova atribuição de endereço IP público. Esse endereço IP público somente é estático durante o período de vida do controlador de entrada e será perdido se o controlador for excluído e recriado. Um requisito de configuração comum é fornecer ao controlador de entrada NGINX um endereço IP público estático existente. O endereço IP público estático permanecerá se o controlador de entrada for excluído. Essa abordagem permite que você use registros DNS existentes e configurações de rede consistentemente durante todo o ciclo de vida dos seus aplicativos.

Se você precisar criar um endereço IP público estático, obtenha primeiro o nome do grupo de recursos do cluster do AKS com o comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Em seguida, crie um endereço IP público com o método de alocação *estático* usando o comando [az network public-ip create][az-network-public-ip-create]. O exemplo a seguir cria um endereço IP público denominado *myAKSPublicIP* no grupo de recursos do cluster AKS obtido na etapa anterior:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Agora, implemente o gráfico *nginx -gresso* com o Helm. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

Você deve passar dois parâmetros adicionais para a versão do Helm para que o controlador de ingestão seja informado tanto do endereço IP estático do balanceador de carga a ser alocado no serviço de controlador de ingestão, quanto do rótulo de nome DNS que está sendo aplicado ao recurso público de endereço IP. Para que os certificados HTTPS funcionem corretamente, um rótulo de nome DNS é usado para configurar um FQDN para o endereço IP do controlador de ingestão.

1. Adicione `--set controller.service.loadBalancerIP` o parâmetro. Especifique seu próprio endereço IP público que foi criado na etapa anterior.
1. Adicione `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` o parâmetro. Especifique um rótulo de nome DNS a ser aplicado ao endereço IP público criado na etapa anterior.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de ingestão. Um seletor de nó é especificado usando o parâmetro `--set nodeSelector` para instruir o agendador do Kubernetes a executar o controlador de entrada NGINX em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamado *ingress-basic*. Especifique um namespace para seu próprio ambiente conforme necessário. Se o cluster AKS não estiver `--set rbac.create=false` habilitado para RBAC, adicione aos comandos Helm.

> [!TIP]
> Se você quiser habilitar a [preservação de IP da][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` fonte do cliente para solicitações de contêineres em seu cluster, adicione ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho de solicitação em *X-Forwarded-For*. Ao usar um controlador de entrada com a preservação de IP da fonte do cliente ativada, a passagem do SSL não funcionará.

Atualize o seguinte script com o **endereço IP** do seu controlador de ingress e um **nome exclusivo** que você gostaria de usar para o prefixo FQDN:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

Quando o serviço de balanceador de carga do Kubernetes é criado para o controlador de entrada NGINX, seu endereço IP estático é atribuído, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Nenhuma regra de ingresso foi criada ainda, portanto, a página 404 padrão do controlador de entrada NGINX será exibida se você navegar para o endereço IP público. As regras de ingresso são configuradas nas etapas a seguir.

Você pode verificar se o rótulo de nome DNS foi aplicado consultando o FQDN no endereço IP público da seguinte forma:

```azurecli-interactive
#!/bin/bash
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query $("[?name=='myAKSPublicIP'].[dnsSettings.fqdn]") -o tsv
```

O controlador de ingress agora está acessível através do endereço IP ou do FQDN.

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada NGINX dá suporte para terminação TLS. Há várias maneiras de recuperar e configurar certificados para HTTPS. Este artigo demonstra como usar o [manager cert][cert-manager], que fornece a geração automática do certificado [Vamos Criptografar][lets-encrypt] e funcionalidade de gerenciamento.

> [!NOTE]
> Este artigo usa o `staging` ambiente para Vamos criptografar. Em implantações de produção, use `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições do recurso e ao instalar o gráfico do Helm.

Para instalar o controlador cert-manager, use o comando `helm install` de instalação Helm a seguir:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Para obter mais informações sobre a configuração docert-manager, consulte o projeto [cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar o emissor de cluster de CA

Antes dos certificados poderem ser emitidos, o cert-manager requer um [Emissor][cert-manager-issuer] ou recurso [ClusterIssuer][cert-manager-cluster-issuer]. Esses recursos Kubernetes são idênticos na funcionalidade porém `Issuer` funcionam em um único namespace onde `ClusterIssuer` funciona em todos os namespaces. Para obter mais informações, consulte a [documentação do emissor do cert-manager][cert-manager-issuer].

Crie um emissor de cluster, como `cluster-issuer.yaml`, usando o manifesto a seguir. Atualize o endereço de email com um endereço válido da sua organização:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Para criar a coleção, use o `kubectl apply -f cluster-issuer.yaml`comando: 

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e uma solução de gerenciamento de certificados foi configurado. Agora, vamos executar duas demonstrações de aplicativos no cluster do AKS. Neste exemplo, o Helm é utilizado para executar várias instâncias de um aplicativo simples hello world.

Antes de instalar os gráficos do Helm de exemplo, adicione o repositório de exemplos do Azure para seu ambiente de Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Em seguida, instale uma segunda instância do aplicativo demo. Para a segunda instância, especifique um novo título para que os dois aplicativos sejam visualmente distintos. Você também precisa especificar um nome de serviço exclusivo:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos estão em execução no cluster de Kubernetes, no entanto, foram configurados com um serviço do tipo `ClusterIP`. Dessa forma, os aplicativos não são acessíveis a partir da Internet. Para torná-los disponíveis publicamente, crie um recurso de entrada de Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é roteado para o serviço `ingress-demo`. Atualize o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` comando.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Criar objeto de certificado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X.509 desejado. Para obter mais informações, consulte [certificados cert-manager][cert-manager-certificates].

Provavelmente, o cert-manager criou automaticamente um objeto de certificado para você usando o ingress-shim, que é implantado automaticamente com o cert-manager desde a v0.2.2. Para obter mais informações, consulte a [Documentação do ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com êxito, use o comando `kubectl describe certificate tls-secret --namespace ingress-basic`.

Se o certificado tiver sido emitido, você verá uma saída semelhante à seguinte:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se você precisar criar um recurso de certificado adicional, pode fazer isso com o manifesto de exemplo a seguir. Atualizar o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior. Se você usar um controlador de entrada somente interno, especifique o nome DNS interno para seu serviço.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Para criar o recurso de certificado, use o `kubectl apply -f certificates.yaml` comando.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador web para o FQDN do seu controlador de *https://demo-aks-ingress.eastus.cloudapp.azure.com*ingress kubernetes, tais como .

Como esses exemplos usam `letsencrypt-staging`, o certificado SSL emitido não é confiável pelo navegador. Aceite a mensagem de aviso para continuar a seu aplicativo. As informações de certificado mostram que este certificado *Fake LE Intermediate X1* é emitido pelo Vamos Criptografar. Esse certificado falso indica que `cert-manager` processou a solicitação corretamente e que foi recebida de um certificado do provedor:

![Certificado de preparo Vamos Criptografar](media/ingress/staging-certificate.png)

Quando você altera Vamos Criptografar para usar `prod` em vez de `staging`, um certificado confiável emitido pelo vamos criptografar é usado, conforme mostrado no exemplo a seguir:

![Certificado Vamos Criptografar](media/ingress/certificate.png)

O aplicativo de demonstração é mostrado no navegador da web:

![Exemplo de aplicativo um](media/ingress/app-one.png)

Agora, adicione o caminho */hello-world-two* para o FQDN, como *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. O segundo aplicativo de demonstração com o título personalizado é mostrado:

![Exemplo de aplicativo dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Este artigo usou o Helm para instalar os componentes de ingresso, os certificados e os aplicativos de exemplo. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir todo o namespace da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Exclua o namespace da amostra e todos os recursos

Para excluir todo o namespace `kubectl delete` da amostra, use o comando e especifique seu nome de namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repo Helm para o aplicativo AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Alternativamente, uma abordagem mais granular é excluir os recursos individuais criados. Primeiro, remova os recursos do certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora liste as versões do Helm com o comando `helm list`. Procure gráficos com o nome *nginx-ingress*, *cert-manager* e *aks-helloworld* conforme mostrado na saída do exemplo a seguir:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Excluir as versões com o `helm uninstall` comando. O exemplo a seguir exclui a implantação de ingresso NGINX, o gerenciador de certificados e os dois aplicativos de exemplo Olá, Mundo do AKS.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

Exclua o próprio namespace. Use `kubectl delete` o comando e especifique seu nome de namespace:

```console
kubectl delete namespace ingress-basic
```

Por fim, remova o endereço IP público estático criado para o controlador de entrada. Forneça seu nome do grupo de recursos de cluster *MC_* obtidos na primeira etapa deste artigo, tal como *MC_meuGrupoDeRecursos_meuClusterCluster_lestedoseua*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]
- [cert-manager][cert-manager]

Também é possível:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada com um IP público dinâmico e configurar Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
