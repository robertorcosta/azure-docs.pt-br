---
title: Instale linkerd no Azure Kubernetes Service (AKS)
description: Saiba como instalar e usar o Linkerd para criar uma malha de serviço em um cluster Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593695"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instale linkerd no Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] é uma malha de serviço de código aberto e [projeto de incubação CNCF.][linkerd-cncf] Linkerd é uma malha de serviçoultraleve que fornece recursos que incluem gerenciamento de tráfego, identidade e segurança de serviço, confiabilidade e observância. Para obter mais informações sobre o Linkerd, consulte a documentação oficial [da Linkerd FAQ][linkerd-faq] e [da Arquitetura Linkerd.][linkerd-architecture]

Este artigo mostra como instalar o Linkerd. O binário `linkerd` cliente Linkerd está instalado na sua máquina cliente e os componentes Linkerd são instalados em um cluster Kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem `stable-2.6.0`referência à versão Linkerd .
>
> O Linkerd `stable-2.6.x` pode ser executado contra `1.13+`as versões kubernetes . Você pode encontrar versões adicionais de linkerd estáveis e de borda no [GitHub - Linkerd Releases][linkerd-github-releases].

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Baixe e instale o linkerd linkerd client binário
> * Instale linkerd em AKS
> * Validar a instalação do Linkerd
> * Acesse o Painel de Controle
> * Desinstalar linkerd da AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo supõem que você criou um `1.13` cluster AKS (Kubernetes e `kubectl` acima, com RBAC ativado) e estabeleceu uma conexão com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart].

Todos os pods linkerd devem ser programados para serem executados em nós Linux - esta configuração é o padrão no método de instalação detalhado abaixo e não requer nenhuma configuração adicional.

Este artigo separa a orientação de instalação do Linkerd em várias etapas discretas. O resultado é o mesmo na estrutura do Linkerd oficial recebendo [orientação][linkerd-getting-started]de início .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instale linkerd em AKS

Antes de instalar o Linkerd, executaremos verificações de pré-instalação para determinar se o plano de controle pode ser instalado em nosso cluster AKS:

```console
linkerd check --pre
```

Você deve ver algo como o seguinte para indicar que seu cluster AKS é um alvo de instalação válido para Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Agora é hora de instalar os componentes linkerd. Use `linkerd` os `kubectl` binários para instalar os componentes Linkerd no cluster AKS. Um `linkerd` namespace será criado automaticamente e os componentes serão instalados neste namespace.

```console
linkerd install | kubectl apply -f -
```

O Linkerd implanta uma série de objetos. Você verá a lista a partir `linkerd install` da saída do seu comando acima. A implantação dos componentes Linkerd deve levar cerca de 1 minuto para ser concluída, dependendo do ambiente de cluster.

Neste ponto, você implantou linkerd para o seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do Linkerd, vamos passar para a próxima seção para [validar a instalação linkerd](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Validar a instalação do Linkerd

Confirme se os recursos foram criados com sucesso. Use os comandos [kubectl get svc][kubectl-get] e [kubectl get pod][kubectl-get] para consultar o `linkerd` namespace, `linkerd install` onde os componentes linkerd foram instalados pelo comando:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

O exemplo a seguir de saída mostra os serviços e pods (agendados em nós Linux) que agora devem estar em execução:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

O Linkerd fornece `linkerd` um comando através do binário do cliente para validar que o plano de controle Linkerd foi instalado e configurado com sucesso.

```console
linkerd check
```

Você deve ver algo como o seguinte para indicar que sua instalação foi bem sucedida:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Acesse o painel de instrumentos

O Linkerd vem com um painel que fornece informações sobre a malha de serviço e as cargas de trabalho. Para acessar o painel, use o `linkerd dashboard` comando. Este comando aproveita o [kubectl port forward][kubectl-port-forward] para criar uma conexão segura entre sua máquina cliente e os pods relevantes em seu cluster AKS. Em seguida, abrirá automaticamente o painel de instrumentos no seu navegador padrão.

```console
linkerd dashboard
```

O comando também criará um port-forward e retornará um link para os painéis Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Desinstalar linkerd da AKS

> [!WARNING]
> A exclusão do Linkerd de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Certifique-se de que você fez provisões para que seu sistema ainda opere corretamente sem o Linkerd antes de prosseguir.

Primeiro você precisará remover os proxies do plano de dados. Remova quaisquer [anotações automáticas][linkerd-automatic-proxy-injection] de injeção de proxy dos espaços de nome de carga de trabalho e implemente suas implantações de carga de trabalho. Suas cargas de trabalho não devem mais ter componentes de plano de dados associados.

Finalmente, remova o plano de controle da seguinte forma:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Próximas etapas

Para explorar mais opções de instalação e configuração para Linkerd, consulte as seguintes orientações oficiais do Linkerd:

- [Linkerd - Instalação do Leme][linkerd-install-with-helm]
- [Linkerd - Instalação em vários estágios para atender privilégios de função][linkerd-multi-stage-installation]

Você também pode seguir cenários adicionais usando:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Demonstração de livros linkerd][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
