---
title: Instale cônsul no Azure Kubernetes Service (AKS)
description: Saiba como instalar e usar o Cônsul para criar uma malha de serviço em um cluster Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273730"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instale e use cônsul no Azure Kubernetes Service (AKS)

[Cônsul][consul-github] é uma malha de serviço de código aberto que fornece um conjunto de funcionalidades chave em todos os microsserviços em um cluster Kubernetes. Esses recursos incluem descoberta de serviços, verificação de saúde, segmentação de serviços e observabilidade. Para mais informações sobre o Cônsul, consulte a documentação oficial [O que é Cônsul?][consul-docs-concepts]

Este artigo mostra como instalar o Cônsul. Os componentes do Cônsul são instalados em um cluster Kubernetes em AKS.

> [!NOTE]
> Estas instruções `1.6.0`referenciam a versão `2.14.2`do Cônsul e usam pelo menos a versão de Helm .
>
> Os lançamentos do Cônsul `1.6.x` podem `1.13+`ser executados contra as versões kubernetes . Você pode encontrar versões adicionais do Cônsul no [GitHub - Liberações do Cônsul][consul-github-releases] e informações sobre cada um dos lançamentos no [Cônsul-Release Notes][consul-release-notes].

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Instale os componentes do Cônsul em AKS
> * Validar a instalação do Cônsul
> * Desinstalar cônsul da AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo supõem que você criou um `1.13` cluster AKS (Kubernetes e `kubectl` acima, com RBAC ativado) e estabeleceu uma conexão com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart]. Certifique-se de que seu cluster tenha pelo menos 3 nós no pool de nós Linux.

Você precisará de [Helm][helm] para seguir estas instruções e instalar cônsul. Recomenda-se que você tenha a versão estável mais recente corretamente instalada e configurada em seu cluster. Se você precisar de ajuda para instalar o Helm, consulte a [orientação de instalação do AKS Helm][helm-install]. Todos os pods cônsultambém devem ser programados para serem executados em nós Linux.

Este artigo separa a orientação de instalação do Cônsul em várias etapas discretas. O resultado final é o mesmo na estrutura da [orientação][consul-install-k8]oficial de instalação do Cônsul.

### <a name="install-the-consul-components-on-aks"></a>Instale os componentes do Cônsul em AKS

Começaremos baixando a `v0.10.0` versão do mapa do Cônsul Helm. Esta versão do gráfico inclui `1.6.0`a versão do Cônsul .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Use Helm e `consul-helm` o gráfico baixado para instalar `consul` os componentes do Cônsul no namespace em seu cluster AKS. 

> [!NOTE]
> **Opções de instalação**
> 
> Estamos usando as seguintes opções como parte de nossa instalação:
> - `connectInject.enabled=true`- permitir que os proxies sejam injetados em vagens
> - `client.enabled=true`- permitir que os clientes cônsules executem em cada nó
> - `client.grpc=true`- habilitar o ouvinte gRPC para conectarInject
> - `syncCatalog.enabled=true`- sincronia serviços Kubernetes e Cônsul
>
> **Seletores de nó**
>
> Atualmente, o Cônsul deve ser programado para ser executado em nós Linux. Se você tiver nós do Windows Server no cluster, você deve garantir que os pods do Cônsul só estejam programados para serem executados em nós Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam programados para os nós corretos.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

O `Consul` gráfico Helm implanta uma série de objetos. Você pode ver a lista `helm install` a partir da saída do seu comando acima. A implantação dos componentes do Cônsul pode levar cerca de 3 minutos para ser concluída, dependendo do ambiente do cluster.

Neste momento, você implantou cônsul para o seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do Cônsul, vamos passar para a próxima seção para validar a instalação do Cônsul.

## <a name="validate-the-consul-installation"></a>Validar a instalação do Cônsul

Confirme se os recursos foram criados com sucesso. Use os comandos [kubectl get svc][kubectl-get] e [kubectl get pod][kubectl-get] para consultar o `consul` namespace, onde os componentes do Cônsul foram instalados pelo `helm install` comando:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

O exemplo a seguir de saída mostra os serviços e pods (agendados em nós Linux) que agora devem estar em execução:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Todas as cápsulas devem mostrar `Running`um status de . Se os pods não tiverem esses status, aguarde um minuto ou dois até que tenham. Se algum pods reportar um problema, use o comando [kubectl describe pod][kubectl-describe] para revisar a saída e o status.

## <a name="accessing-the-consul-ui"></a>Acessando a UI Cônsul

A UI cônsul foi instalada em nossa configuração acima e fornece configuração baseada em UI para Cônsul. A UI para Cônsul não é exposta publicamente através de um endereço IP externo. Para acessar a interface de usuário do Cônsul, use o comando [kubectl port-forward.][kubectl-port-forward] Este comando cria uma conexão segura entre sua máquina cliente e o pod relevante em seu cluster AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Agora você pode abrir um `http://localhost:8080/ui` navegador e apontá-lo para abrir a Interface do Cônsul. Você deve ver o seguinte quando abrir a ui:

![Cônsul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Desinstalar cônsul da AKS

> [!WARNING]
> A exclusão do Cônsul de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Certifique-se de que você fez provisões para que seu sistema ainda opere corretamente sem o Cônsul antes de prosseguir.

### <a name="remove-consul-components-and-namespace"></a>Remova os componentes do cônsul e o namespace

Para remover o Cônsul do seu cluster AKS, use os seguintes comandos. Os `helm delete` comandos removerão o `consul` `kubectl delete namespace` gráfico e `consul` o comando removerá o namespace.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Próximas etapas

Para explorar mais opções de instalação e configuração para o Cônsul, consulte os seguintes artigos oficiais do Cônsul:

- [Cônsul - Guia de instalação do Leme][consul-install-k8]
- [Cônsul - Opções de instalação do Leme][consul-install-helm-options]

Você também pode seguir cenários adicionais usando:

- [Aplicativo exemplo cônsul][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
