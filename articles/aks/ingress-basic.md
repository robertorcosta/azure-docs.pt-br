---
title: Crie um controlador de entrada básico no AKS (Serviço de Kubernetes do Azure)
description: Aprenda a instalar e configurar um controlador de entrada NGINX básico em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: e37f7aa677be129aa9fe568880c53cc860947e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595630"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada no AKS (Serviço de Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) Dois aplicativos são então executados no cluster do AKS, cada um dos quais é acessível pelo endereço IP único.

Também é possível:

- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que use Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o controlador de ingresso NGINX e um aplicativo da web de amostra.

Este artigo também exige que você esteja executando a versão 2.0.64 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use o Helm para instalar *a ingestão de nginx*. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de ingestão. Um seletor de nó é especificado usando o parâmetro `--set nodeSelector` para instruir o agendador do Kubernetes a executar o controlador de entrada NGINX em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamado *ingress-basic*. Especifique um namespace para seu próprio ambiente conforme necessário.

> [!TIP]
> Se você quiser habilitar a [preservação de IP da][client-source-ip] `--set controller.service.externalTrafficPolicy=Local` fonte do cliente para solicitações de contêineres em seu cluster, adicione ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho de solicitação em *X-Forwarded-For*. Ao usar um controlador de entrada com a preservação de IP da fonte do cliente ativada, a passagem do SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balanceador de carga do Kubernetes é criado para o controlador de entrada NGINX, um endereço IP público dinâmico é atribuído, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Nenhuma regra de ingresso foi criada ainda, portanto, a página 404 padrão do controlador de ingresso NGINX é exibida se você navega para o endereço IP interno. As regras de ingresso são configuradas nas etapas a seguir.

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Para ver o controlador de entrada em ação, executaremos dois aplicativos de demonstração no cluster do AKS. Neste exemplo, helm é usado para implantar duas instâncias de um simples aplicativo *mundial Hello.*

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
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos agora estão em execução no cluster do Kubernetes. Para rotear o tráfego para cada aplicativo, crie um recurso de entrada do Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para `aks-helloworld` *EXTERNAL_IP* é encaminhado para o serviço nomeado . O tráfego para *EXTERNAL_IP/hello-world-two* `aks-helloworld-two` é encaminhado para o serviço. O tráfego para *EXTERNAL_IP/estática* é `aks-helloworld` encaminhado para o serviço nomeado para ativos estáticos.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Testar o controlador de entrada

Para testar as rotas para o controlador de entrada, navegue até os dois aplicativos. Abra um navegador web para o endereço IP do seu controlador de ingestão NGINX, como *EXTERNAL_IP*. O primeiro aplicativo de demonstração é exibido no navegador da Web, conforme mostrado no exemplo a seguir:

![Primeiro aplicativo em execução por trás do controlador de entrada](media/ingress-basic/app-one.png)

Agora adicione o caminho */hello-world-two* ao endereço IP, como *EXTERNAL_IP/hello-world-two*. O segundo aplicativo de demonstração com o título personalizado é mostrado:

![Segundo aplicativo em execução por trás do controlador de entrada](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso e os aplicativos de amostra. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir todo o namespace da amostra ou os recursos individuais.

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

Alternativamente, uma abordagem mais granular é excluir os recursos individuais criados. Liste os lançamentos `helm list` do Helm com o comando. Procure gráficos nomeados *nginx -gresso* e *aks-helloworld*, conforme mostrado na saída do exemplo a seguir:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Excluir as versões com o `helm delete` comando. O exemplo a seguir exclui a implantação de ingresso NGINX e os dois aplicativos exemplo AKS hello world.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Finalmente, você pode excluir o próprio namespace. Use `kubectl delete` o comando e especifique seu nome de namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]

Também é possível:

- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que use Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
