---
title: Instalar Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e usar o Istio para criar uma malha de serviço em um cluster do AKS (Serviço de Kubernetes do Azure)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877687"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, consulte a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como instalar o Istio. O binário cliente Istio `istioctl` é instalado na sua máquina cliente e os componentes Istio são instalados em um cluster Kubernetes no AKS.

> [!NOTE]
> As seguintes instruções `1.4.0`fazem referência à versão de Istio .
>
> Os lançamentos `1.4.x` de Istio foram testados pela equipe `1.13`Istio contra as versões kubernetes, `1.14`. `1.15` Você pode encontrar versões adicionais do Istio no [GitHub - Istio Releases][istio-github-releases], informações sobre cada um dos lançamentos no [Istio News][istio-release-notes] e versões do Kubernetes suportadas no [Istio General FAQ][istio-faq].

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Baixe e instale o binário do cliente Istio istioctl
> * Instale istio em AKS
> * Validar a instalação istio
> * Acesse os complementos
> * Desinstalar istio da AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo supõem que você criou um `1.13` cluster AKS (Kubernetes e `kubectl` acima, com RBAC ativado) e estabeleceu uma conexão com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart].

Certifique-se de ter lido a documentação [istio performance e escalabilidade](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos adicionais de recursos para executar istio em seu cluster AKS. Os requisitos de núcleo e memória variam de acordo com sua carga de trabalho específica. Escolha um número apropriado de nós e tamanho VM para atender à sua configuração.

Este artigo separa as diretrizes de instalação do Istio em várias etapas discretas. O resultado final é o mesmo na estrutura das [diretrizes][istio-install-istioctl] de instalação do Istio oficial.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Instale os componentes Istio no AKS

Vamos instalar [Grafana][grafana] e [Kiali][kiali] como parte da nossa instalação istio. A Grafana fornece painéis de análise e monitoramento, e a Kiali fornece um painel de observação de malha de serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como [um segredo][kubernetes-secrets].

Antes de instalarmos os componentes istio, devemos criar os segredos tanto para Grafana quanto para Kiali. Esses segredos precisam ser instalados no `istio-system` namespace que será usado por Istio, então vamos precisar criar o namespace também. Precisamos usar a `--save-config` opção ao criar `kubectl create` o namespace via para que `kubectl apply` o instalador Istio possa ser executado neste objeto no futuro.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Instale componentes Istio

Agora que criamos com sucesso os segredos Grafana e Kiali em nosso cluster AKS, é hora de instalar os componentes istio. 

A abordagem de instalação [do Helm][helm] para Istio será preterida no futuro. A nova abordagem de instalação `istioctl` da Istio aproveita o binário do cliente, os [perfis de configuração istio][istio-configuration-profiles]e a nova [especificação do plano de controle Istio e aapi][istio-control-plane]. Esta nova abordagem é o que vamos usar para instalar istio.

> [!NOTE]
> Istio atualmente deve ser programado para ser executado em nós Linux. Se você tiver nós do Windows Server em seu cluster, você deve garantir que os pods Istio só estejam programados para serem executados em nós Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam programados para os nós corretos.

> [!CAUTION]
> Os [recursos do SDS (serviço de descoberta secreta)][istio-feature-sds] e [istio CNI][istio-feature-cni] Istio estão atualmente em [Alpha][istio-feature-stages], então o pensamento deve ser dado antes de habilitá-los. 
>
> Observe que o recurso [Service Account Token Volume Projection][kubernetes-feature-sa-projected-volume] Kubernetes (um requisito para SDS) está agora **habilitado** para todas as versões Kubernetes 1.13 e superiores no AKS.

Crie um `istio.aks.yaml` arquivo chamado com o seguinte conteúdo. Este arquivo manterá os [detalhes da especificação do plano de controle Istio][istio-control-plane] para configurar istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Instale istio `istioctl apply` usando o `istio.aks.yaml` comando e o arquivo de especificação do plano de controle Istio acima da seguinte forma:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

O instalador implantará uma série de [CRDs][kubernetes-crd] e, em seguida, gerenciará dependências para instalar todos os objetos relevantes definidos para esta configuração do Istio. Você deve ver algo como o seguinte trecho de saída.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Neste ponto, você implantou Istio para o seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do Istio, vamos passar para a próxima seção para [validar a instalação do Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação istio

Primeiro, confirme se os serviços esperados foram criados. Use o comando [kubectl get svc][kubectl-get] para exibir os serviços em execução. Consultar o `istio-system` namespace, onde os componentes Istio e add-on foram instalados pelo `istio` gráfico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que devem estar em execução agora:

- Serviços do `istio-*`
- `jaeger-*`, `tracing`e `zipkin` serviços de rastreamento de complementos
- `prometheus`serviço de métricas adicionais
- `grafana`serviço de análise de complemento e monitoramento do painel
- `kiali`serviço de malha de serviço complemento serviço serviço

Se `istio-ingressgateway` mostrar um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP seja atribuído pela rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Em seguida, confirme se os pods necessários foram criados. Use o comando [kubectl get pods][kubectl-get] `istio-system` e consulte novamente o namespace:

```console
kubectl get pods --namespace istio-system
```

A saída do exemplo a seguir mostra os pods em execução:

- as `istio-*` vagens
- o `prometheus-*` pod de métricas adicionais
- o `grafana-*` painel de análise e monitoramento de análise sinuoso
- o `kiali` painel de malha de serviço adicionais pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Todas as cápsulas devem mostrar `Running`um status de . Se os pods não tiverem esses status, aguarde um minuto ou dois até que tenham. Se algum pods reportar um problema, use o comando [kubectl describe pod][kubectl-describe] para revisar a saída e o status.

## <a name="accessing-the-add-ons"></a>Acessar os complementos

Uma série de complementos foram instalados pela Istio em nossa configuração acima que fornecem funcionalidade adicional. Os aplicativos web para os complementos **não** são expostos publicamente através de um endereço ip externo. 

Para acessar as interfaces de usuário `istioctl dashboard` adicionais, use o comando. Este comando aproveita [a porta kubectl][kubectl-port-forward] e uma porta aleatória para criar uma conexão segura entre sua máquina cliente e o pod relevante em seu cluster AKS. Em seguida, abrirá automaticamente o aplicativo da Web no seu navegador padrão.

Adicionamos uma camada adicional de segurança para Grafana e Kiali, especificando credenciais para eles no início deste artigo.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitoramento da Istio são fornecidos pela [Grafana][grafana]. Lembre-se de usar as credenciais que você criou através do segredo grafana mais cedo quando solicitado. Abra o painel grafana com segurança da seguinte forma:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

As métricas para Istio são fornecidas por [Prometeu][prometheus]. Abra o painel Prometheus com segurança da seguinte forma:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

O rastreamento dentro de Istio é fornecido por [Jaeger.][jaeger] Abra o painel Jaeger com segurança:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Um painel de observabilidade de malha de serviço é fornecido por [Kiali][kiali]. Lembre-se de usar as credenciais que você criou através do segredo Kiali mais cedo quando solicitado. Abra o painel kiali com segurança da seguinte forma:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Uma interface simples para os proxies [Envoy][envoy] está disponível. Ele fornece informações de configuração e métricas para um proxy envoy em execução em um pod especificado. Abra a interface do Enviado com segurança da seguinte forma:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Desinstalar istio da AKS

> [!WARNING]
> A exclusão do Istio de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Certifique-se de que você fez provisões para que seu sistema ainda opere corretamente sem istio antes de prosseguir.

### <a name="remove-istio-components-and-namespace"></a>Remova os componentes istio e o namespace

Para remover istio do seu cluster `istioctl manifest generate` AKS, use o comando com o `istio.aks.yaml` arquivo de especificação do plano de controle Istio. Isso irá gerar o manifesto implantado, `kubectl delete` que vamos canalizar para remover todos `istio-system` os componentes instalados e o namespace.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Remover Istio CRDs e Segredos

Os comandos acima excluem todos os componentes istio e namespace, mas ainda ficamos com segredos Istio gerados. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode usar istio para fornecer roteamento inteligente para implementar uma versão canária:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente do Istio do AKS][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para Istio, consulte as seguintes orientações oficiais da Istio:

- [Istio - guias de instalação][istio-installation-guides]

Você também pode seguir cenários adicionais usando:

- [Exemplo de aplicativo Istio Bookinfo][istio-bookinfo-example]

Para saber como monitorar seu aplicativo AKS usando o Application Insights e o Istio, consulte a documentação a seguir do Azure Monitor:

- [Monitoramento de aplicativos de instrumentação zero para aplicativos hospedados do Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
