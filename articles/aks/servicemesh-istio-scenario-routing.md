---
title: Roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Istio para fornecer roteamento inteligente e implantar versões canário em um cluster do AKS (Serviço de Kubernetes do Azure)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78272999"
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
> Este cenário foi testado contra `1.3.2`a versão de Istio.

As etapas detalhadas neste artigo supõem que você criou `1.13` um cluster AKS (Kubernetes `kubectl` e acima, com RBAC ativado) e estabeleceu uma conexão com o cluster. Você também precisará do Istio instalado em seu cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o [AKS quickstart][aks-quickstart] e instale Istio na orientação [AKS.][istio-install]

## <a name="about-this-application-scenario"></a>Sobre esse cenário de aplicativo

O aplicativo de votação aks de exemplo fornece duas opções de votação (**Gatos** ou **Cães**) aos usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos convertidos para cada opção.

Neste cenário de aplicativo, você `1.0` começa implantando a `1.0` versão do aplicativo de votação e a versão do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e `1.0` o componente de análise interagem com a versão do componente de armazenamento, que é apoiado pelo Redis.

Você atualiza o componente `1.1`de análise para a versão, que fornece contagem e agora totais e percentuais.

Um subconjunto de `2.0` usuários testam a versão do aplicativo através de uma versão canária. Essa nova versão usa um componente de armazenamento que tem suporte de um banco de dados MySQL.

Uma vez que você `2.0` está confiante de que a versão funciona `2.0` como esperado em seu subconjunto de usuários, você implementa a versão para todos os seus usuários.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Vamos começar pela implantação do aplicativo no cluster do AKS (Serviço de Kubernetes do Azure). O diagrama a seguir mostra o que `1.0` é executado até o final desta seção - versão de todos os componentes com solicitações de entrada atendidas através do gateway de entrada istio:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefatos necessários para acompanhar este artigo estão disponíveis no repositório do GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. É possível baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório clonado / baixado e execute todas as etapas subsequentes nesta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace no cluster AKS para o `voting` aplicativo de votação AKS de exemplo nomeado da seguinte forma:

```console
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Esse rótulo instrui o Istio para injetar automaticamente os istio-proxies como sidecars em todos os pods nesse namespace.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para o aplicativo de votação do AKS. Crie esses componentes `voting` no namespace criado em uma etapa anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

O exemplo de saída a seguir mostra os recursos que estão sendo criados:

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

O exemplo a seguir mostra que `voting-app` há três instâncias `voting-analytics` do `voting-storage` pod e uma única instância de ambos os pods. Cada um dos pods tem dois contêineres. Um desses recipientes é o componente, `istio-proxy`e o outro é o:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver informações sobre o pod, usaremos o comando [kubectl][kubectl-describe] describe `voting-analytics` pod com seletores de rótulos para selecionar o pod. Filtraremos a saída para mostrar os detalhes dos dois recipientes presentes no pod:

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

Use `kubectl apply` o comando para implantar o yaml Gateway e o Serviço Virtual. Lembre-se de especificar o namespace em que esses recursos são implantados.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

O exemplo a seguir mostra o novo Gateway e serviço virtual sendo criado:

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

As informações na parte inferior da tela `1.0` mostram `voting-app` que `1.0` `voting-storage` o aplicativo usa versão e versão de (Redis).

## <a name="update-the-application"></a>Atualizar o aplicativo

Vamos implantar uma nova versão do componente de análise. Esta nova `1.1` versão exibe totais e percentuais, além da contagem para cada categoria.

O diagrama a seguir mostra o que será `1.1` executado `voting-analytics` no final desta seção - apenas a versão do nosso componente tem o tráfego roteado a partir do `voting-app` componente. Embora a `1.0` versão `voting-analytics` do nosso componente continue a `voting-analytics` ser executada e seja referenciada pelo serviço, os proxies istio não permitem tráfego de e para ele.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implantar a `1.1` versão `voting-analytics` do componente. Crie este componente `voting` no namespace:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

O exemplo de saída a seguir mostra os recursos que estão sendo criados:

```output
deployment.apps/voting-analytics-1-1 created
```

Abra novamente o aplicativo de votação de exemplo do AKS em um navegador, usando o endereço IP do gateway de entrada do Istio obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Uma vez que você está [Service][kubernetes-service] usando `voting-analytics` um Serviço Kubernetes`app: voting-analytics`para o componente com apenas um único seletor de rótulos ( ), kubernetes usa o comportamento padrão de round-robin entre os pods que correspondem a esse seletor. Neste caso, é tanto `1.0` `1.1` a `voting-analytics` versão quanto de seus pods.

![A versão 1.0 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar a comutação entre `voting-analytics` as duas versões do componente da seguinte forma. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio.

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

Agora vamos bloquear o tráfego `1.1` apenas `voting-analytics` para a `1.0` versão `voting-storage` do componente e para a versão do componente. Em seguida, você define regras de roteamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais métodos de autenticação podem ser aceitos em cargas de trabalho.

Use `kubectl apply` o comando para substituir a `voting-app` definição de Serviço Virtual em seu e adicione [Regras de Destino][istio-reference-destinationrule] e [Serviços Virtuais][istio-reference-virtualservice] para os outros componentes. Você adicionará [Policy][istio-reference-policy] uma `voting` Diretiva ao namespace para garantir que toda a comunicação entre serviços seja garantida usando TLS mútuo suste e certificados de cliente.

* A Diretiva `peers.mtls.mode` foi `STRICT` definida para garantir que o TLS `voting` mútuo seja aplicado entre seus serviços dentro do namespace.
* Nós também `trafficPolicy.tls.mode` definimos o para em `ISTIO_MUTUAL` todas as nossas Regras de Destino. O Istio fornece serviços com identidades fortes e protege as comunicações entre serviços usando a TLS mútua e certificados do cliente que o Istio gerencia de forma transparente.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

O exemplo a seguir mostra a nova Política, Regras de destino e serviços virtuais sendo atualizados/criados:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se você abrir o aplicativo AKS Voting em `1.1` um `voting-analytics` navegador novamente, `voting-app` apenas a nova versão do componente será usada pelo componente.

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar que agora você está `1.1` apenas `voting-analytics` encaminhado para a versão do seu componente da seguinte forma. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio:

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

Vamos agora confirmar que Istio está usando TLS mútuo para garantir comunicações entre cada um de nossos serviços. Para isso, usaremos o comando [authn tls-check][istioctl-authn-tls-check] no binário do `istioctl` cliente, que assume a forma a seguir.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Este conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todos os pods que estão em um namespace e correspondem a um conjunto de rótulos:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

O exemplo a seguir mostra que o TLS mútuo é aplicado para cada uma de nossas consultas acima. A saída também mostra as Regras de Política e destino que impõem o TLS mútuo:

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

Agora vamos implantar uma `2.0` nova `voting-app`versão `voting-analytics`dos `voting-storage` componentes e componentes. O `voting-storage` novo componente usa MySQL em `voting-app` vez `voting-analytics` de Redis, e os `voting-storage` componentes são atualizados para permitir que eles usem este novo componente.

O `voting-app` componente agora suporta a funcionalidade do recurso de sinalizador. Esse sinalizador de recursos permite que você teste a capacidade da versão canário do Istio para um subconjunto de usuários.

O diagrama a seguir mostra o que você terá em execução no final desta seção.

* A `1.0` versão `voting-app` do `1.1` componente, `voting-analytics` a `1.0` versão `voting-storage` do componente e a versão do componente são capazes de se comunicar entre si.
* A `2.0` versão `voting-app` do `2.0` componente, `voting-analytics` a `2.0` versão `voting-storage` do componente e a versão do componente são capazes de se comunicar entre si.
* A `2.0` versão `voting-app` do componente só é acessível a usuários que tenham um conjunto de pontos específicos. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-03.png)

Primeiro, atualize as regras de destino do Istio e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não roteie tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

O exemplo a seguir mostra as regras de destino e serviços virtuais sendo atualizados:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos Kubernetes para os novos componentes da versão. `2.0` Você também `voting-storage` atualiza o `3306` serviço para incluir a porta para MySQL:

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

Aguarde até `2.0` que todas as versões estejam em execução. Use o comando [kubectl get pods][kubectl-get] com o interruptor de `-w` `voting` relógio para observar as alterações em todas as cápsulas no namespace:

```console
kubectl get pods --namespace voting -w
```

Agora você deve ser capaz `1.0` de `2.0` alternar entre a versão e a versão (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Este cookie é `voting-app` usado pelo Serviço Virtual para `2.0`direcionar os usuários para a nova versão .

![A versão 1.0 do aplicativo de votação do AKS - sinalizador de recurso NÃO ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![A versão 2.0 do aplicativo de votação do AKS - sinalizador de recurso ESTÁ definida.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença destaca que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Depois de testar com sucesso a versão `voting-app` canária, atualize `2.0` o `voting-app` Serviço Virtual para direcionar todo o tráfego para a versão do componente. Todos os usuários `2.0` então vêem a versão do aplicativo, independentemente de o sinalizador de recurso estar definido ou não:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que não quer mais ativas. Em seguida, atualize todos os serviços virtuais para parar de fazer referência a essas versões.

Como não há mais nenhum tráfego para qualquer uma das versões mais antigas dos componentes, agora é possível excluir com segurança todas as implantações para esses componentes.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/servicemesh/istio/scenario-routing-components-05.png)

Você agora distribuiu com êxito uma nova versão do aplicativo de votação do AKS.

## <a name="clean-up"></a>Limpar 

Você pode remover o aplicativo de votação AKS que usamos neste `voting` cenário a partir do seu cluster AKS excluindo o namespace da seguinte forma:

```console
kubectl delete namespace voting
```

A saída do exemplo a seguir mostra que todos os componentes do aplicativo de votação AKS foram removidos do seu cluster AKS.

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
