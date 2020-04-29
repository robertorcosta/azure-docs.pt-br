---
title: Usar İSTİO para roteamento inteligente
titleSuffix: Azure Kubernetes Service
description: Saiba como usar o Istio para fornecer roteamento inteligente e implantar versões canário em um cluster do AKS (Serviço de Kubernetes do Azure)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668283"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usar roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, consulte a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como usar a funcionalidade de gerenciamento de tráfego do Istio. Um aplicativo de votação de exemplo do AKS é usado para explorar o roteamento inteligente e as versões canário.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Implantar o aplicativo
> * Atualizar o aplicativo
> * Distribuir uma versão canário do aplicativo
> * Finalizar a distribuição

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este cenário foi testado em relação à versão `1.3.2`İSTİO.

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o RBAC habilitado) e estabeleceu `kubectl` uma conexão com o cluster. Você também precisará do İSTİO instalado no cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart] e [Instale o İSTİO no AKs][istio-install] Guidance.

## <a name="about-this-application-scenario"></a>Sobre esse cenário de aplicativo

O aplicativo de votação AKS de exemplo fornece duas opções de votação (**gatos** ou **cachorros**) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos convertidos para cada opção.

Neste cenário de aplicativo, você começa implantando a `1.0` versão do aplicativo de votação e `1.0` a versão do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise `1.0` interagem com a versão do componente de armazenamento, que é apoiada por Redis.

Você atualiza o componente de análise para `1.1`a versão, que fornece contagens e agora os totais e percentuais.

Um subconjunto da versão `2.0` de teste dos usuários do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que tem suporte de um banco de dados MySQL.

Quando tiver certeza de que a `2.0` versão funciona conforme o esperado em seu subconjunto de usuários, distribua `2.0` a versão para todos os usuários.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Vamos começar pela implantação do aplicativo no cluster do AKS (Serviço de Kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção – `1.0` versão de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do İSTİO:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefatos necessários para acompanhar este artigo estão disponíveis no repositório do GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. É possível baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório clonado / baixado e execute todas as etapas subsequentes nesta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace em seu cluster AKS para o aplicativo de votação AKS de `voting` exemplo chamado da seguinte maneira:

```console
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Esse rótulo instrui o Istio para injetar automaticamente os istio-proxies como sidecars em todos os pods nesse namespace.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para o aplicativo de votação do AKS. Crie esses componentes no `voting` namespace criado em uma etapa anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> O Istio tem alguns requisitos específicos acerca de pods e serviços. Para obter mais informações, consulte o [Requisitos dos Istio para a documentação de Pods e Serviços][istio-requirements-pods-and-services].

Para ver os pods que foram criados, use o comando [kubectl get pods][kubectl-get] como a seguir:

```console
kubectl get pods -n voting --show-labels
```

A saída de exemplo a seguir mostra que há três instâncias `voting-app` do pod e uma única instância de `voting-analytics` e `voting-storage` pods. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente e o outro é o `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver informações sobre o Pod, usaremos o comando [Pod kubectl para descrever][kubectl-describe] os seletores de rótulo para selecionar o `voting-analytics` Pod. Filtraremos a saída para mostrar os detalhes dos dois contêineres presentes no pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Não é possível conectar ao aplicativo de votação até que você crie o [Gateway][istio-reference-gateway] e o [Serviço Virtual][istio-reference-virtualservice] do Istio. Esses recursos do Istio roteiam o tráfego do gateway de entrada padrão do Istio para nosso aplicativo.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
> 
> Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o `kubectl apply` comando para implantar o gateway e o YAML do serviço virtual. Lembre-se de especificar o namespace no qual esses recursos são implantados.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra o novo gateway e o serviço virtual que está sendo criado:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do gateway de entrada do Istio usando o seguinte comando:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra os endereços IP do gateway de entrada:

```output
20.188.211.19
```

Abra um navegador e cole o endereço IP. O aplicativo de votação de exemplo do AKS é exibido.

![O aplicativo de votação do AKS em execução no Istio habilitou o cluster do AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão `1.0` do `voting-app` e a `1.0` versão `voting-storage` do (Redis).

## <a name="update-the-application"></a>Atualizar o aplicativo

Vamos implantar uma nova versão do componente de análise. Essa nova versão `1.1` exibe os totais e as porcentagens, além da contagem de cada categoria.

O diagrama a seguir mostra o que será executado no final desta seção-somente a versão `1.1` do nosso `voting-analytics` componente tem o tráfego roteado `voting-app` do componente. Embora a versão `1.0` do nosso `voting-analytics` componente continue a ser executada e seja referenciada `voting-analytics` pelo serviço, os proxies İSTİO não permitem o tráfego de e para ela.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implantar a `voting-analytics` versão `1.1` do componente. Crie este componente no `voting` namespace:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```output
deployment.apps/voting-analytics-1-1 created
```

Abra novamente o aplicativo de votação de exemplo do AKS em um navegador, usando o endereço IP do gateway de entrada do Istio obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um [serviço][kubernetes-service] kubernetes para o `voting-analytics` componente com apenas um seletor de rótulo`app: voting-analytics`(), kubernetes usa o comportamento padrão de Round Robin entre os pods que correspondem a esse seletor. Nesse caso, é a versão `1.0` e `1.1` o `voting-analytics` pods.

![A versão 1.0 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar a alternância entre as duas versões do `voting-analytics` componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site retornada como as alternâncias de site entre as versões:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1.1 do aplicativo

Agora, vamos bloquear o tráfego para apenas a `1.1` versão do `voting-analytics` componente e para a `1.0` versão do `voting-storage` componente. Em seguida, você define regras de roteamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais métodos de autenticação podem ser aceitos em cargas de trabalho.

Use o `kubectl apply` comando para substituir a definição de serviço virtual em `voting-app` seu e adicionar [regras de destino][istio-reference-destinationrule] e [Serviços virtuais][istio-reference-virtualservice] para os outros componentes. Você adicionará uma [política][istio-reference-policy] ao `voting` namespace para garantir que todas as comunicações entre serviços sejam protegidas usando TLS mútuo e certificados de cliente.

* A política foi `peers.mtls.mode` definida como `STRICT` para garantir que o TLS mútuo seja imposto entre os serviços no `voting` namespace.
* Também definimos o `trafficPolicy.tls.mode` como `ISTIO_MUTUAL` em todas as nossas regras de destino. O Istio fornece serviços com identidades fortes e protege as comunicações entre serviços usando a TLS mútua e certificados do cliente que o Istio gerencia de forma transparente.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra a nova política, as regras de destino e os serviços virtuais que estão sendo atualizados/criados:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se você abrir o aplicativo de votação AKS em um navegador novamente, somente a nova `1.1` versão do `voting-analytics` componente será usada pelo `voting-app` componente.

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar que agora é roteado apenas para a `1.1` versão do `voting-analytics` seu componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site retornada:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Agora, vamos confirmar se o İSTİO está usando o TLS mútuo para proteger as comunicações entre cada um de nossos serviços. Para isso, usaremos o comando [Authn TLS-check][istioctl-authn-tls-check] no binário do `istioctl` cliente, que usa o seguinte formulário.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Esse conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todos os pods que estão em um namespace e correspondem a um conjunto de rótulos:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra que o TLS mútuo é imposto para cada uma de nossas consultas acima. A saída também mostra as regras de política e destino que impõe o TLS mútuo:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuir uma versão canário do aplicativo

Agora, vamos implantar uma nova versão `2.0` dos componentes `voting-app`, `voting-analytics`e `voting-storage` . O novo `voting-storage` componente usa o MySQL em vez do Redis, `voting-app` e `voting-analytics` os componentes e são atualizados para permitir que eles usem `voting-storage` esse novo componente.

O `voting-app` componente agora dá suporte à funcionalidade de sinalizador de recursos. Esse sinalizador de recursos permite que você teste a capacidade da versão canário do Istio para um subconjunto de usuários.

O diagrama a seguir mostra o que você terá em execução no final desta seção.

* Versão `1.0` `voting-app` do componente, versão `1.1` do `voting-analytics` componente e versão `1.0` do `voting-storage` componente, são capazes de se comunicar entre si.
* Versão `2.0` `voting-app` do componente, versão `2.0` do `voting-analytics` componente e versão `2.0` do `voting-storage` componente, são capazes de se comunicar entre si.
* A `2.0` `voting-app` versão do componente só pode ser acessada por usuários que têm um sinalizador de recurso específico definido. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-03.png)

Primeiro, atualize as regras de destino do Istio e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não roteie tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra as regras de destino e os serviços virtuais que estão sendo atualizados:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos kubernetes para os novos componentes `2.0` de versão. Você também atualiza o `voting-storage` serviço para incluir a `3306` porta para MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos do Kubernetes foram criados ou atualizados com êxito:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os `2.0` pods de versão estejam em execução. Use o comando [kubectl Get pods][kubectl-get] com a `-w` opção Watch para observar as alterações em todos os pods no `voting` namespace:

```console
kubectl get pods --namespace voting -w
```

Agora você deve ser capaz de alternar entre a versão `1.0` e a `2.0` versão (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelo serviço `voting-app` virtual para rotear usuários para a nova versão `2.0`.

![A versão 1.0 do aplicativo de votação do AKS - sinalizador de recurso NÃO ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![A versão 2.0 do aplicativo de votação do AKS - sinalizador de recurso ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença destaca que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Depois de testar com êxito a versão do canário, atualize o `voting-app` serviço virtual para rotear todo o tráfego `2.0` para a `voting-app` versão do componente. Em seguida, todos os `2.0` usuários veem a versão do aplicativo, independentemente de o sinalizador de recurso ser definido ou não:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que não quer mais ativas. Em seguida, atualize todos os serviços virtuais para parar de fazer referência a essas versões.

Como não há mais nenhum tráfego para qualquer uma das versões mais antigas dos componentes, agora é possível excluir com segurança todas as implantações para esses componentes.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-05.png)

Você agora distribuiu com êxito uma nova versão do aplicativo de votação do AKS.

## <a name="clean-up"></a>Limpar 

Você pode remover o aplicativo de votação AKS que usamos neste cenário do cluster AKS excluindo o `voting` namespace da seguinte maneira:

```console
kubectl delete namespace voting
```

A saída de exemplo a seguir mostra que todos os componentes do aplicativo de votação AKS foram removidos do cluster AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Próximas etapas

Você pode explorar cenários adicionais usando o [Exemplo de Aplicação do Bookinfo do Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
