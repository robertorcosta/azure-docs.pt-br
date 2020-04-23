---
title: Instale aplicativos existentes com o Helm em AKS
description: Saiba como usar a ferramenta de embalagem Helm para implantar contêineres em um cluster Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870245"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instale aplicativos existentes com o Helm no Azure Kubernetes Service (AKS)

[Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como *APT* e *Yum*, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados.

Este artigo mostra como configurar e usar o Helm em um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você também precisa do Helm CLI instalado, que é o cliente que funciona em seu sistema de desenvolvimento. Ele permite que você inicie, pare e gerencie aplicativos com o Helm. Se você usa o Azure Cloud Shell, o a CLI do Helm já está instalada. Para obter instruções de instalação em sua plataforma local, consulte [Instalação de Leme][helm-install].

> [!IMPORTANT]
> Helm é destinado a executar em nós Linux. Se você tiver nós do Windows Server em seu cluster, você deve garantir que os pods helm só estão programados para serem executados em nós Linux. Você também precisa garantir que todos os gráficos do Helm instalados também sejam programados para serem executados nos nós corretos. Os comandos deste artigo usam [seletores de][k8s-node-selector] nó para garantir que os pods sejam programados para os nós corretos, mas nem todos os gráficos helm podem expor um seletor de nós. Você também pode considerar o uso de outras opções em seu cluster, como [manchas][taints].

## <a name="verify-your-version-of-helm"></a>Verifique sua versão do Helm

Use `helm version` o comando para verificar a versão do Helm que você instalou:

```console
helm version
```

O exemplo a seguir mostra a versão 3.0.0 instalada do Helm:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Para Helm v3, siga os passos na [seção Helm v3](#install-an-application-with-helm-v3). Para Helm v2, siga os passos na [seção Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Instale um aplicativo com Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Adicione o repositório oficial de gráficos estáveis helm

Use o comando [helm repo][helm-repo-add] para adicionar o repositório oficial de gráficos estáveis Helm.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Localizar gráficos Helm

Os gráficos do Helm são usados para implantar aplicativos em um cluster Kubernetes. Para procurar gráficos helm pré-criados, use o comando [helm search:][helm-search]

```console
helm search repo stable
```

A saída de exemplo condensada a seguir mostra alguns dos gráficos do Helm disponíveis para uso:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Para atualizar a lista de gráficos, use o comando [helm repo update][helm-repo-update]. O exemplo a seguir mostra uma atualização de repositório bem-sucedida:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Executar gráficos Helm

Para instalar gráficos com helm, use o comando [helm install][helm-install-command] e especifique um nome de lançamento e o nome do gráfico a ser instalado. Para ver a instalação de um gráfico Helm em ação, vamos instalar uma implantação básica nginx usando um gráfico Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A saída do exemplo condensado a seguir mostra o status de implantação dos recursos do Kubernetes criados pelo gráfico do Helm:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Use `kubectl get services` o comando para obter *o EXTERNAL-IP* do seu serviço. Por exemplo, o comando abaixo mostra o *EXTERNAL-IP* para o serviço *my-nginx-ingress-controller:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lançamentos de listas

Para ver uma lista de versões instaladas no cluster, use o `helm list` comando.

```console
helm list
```

O exemplo a seguir mostra a versão *my-nginx-ingress* s implantado na etapa anterior:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Limpar os recursos

Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, use o comando [helm desinstalar][helm-cleanup] e `helm list` especifique seu nome de lançamento, como encontrado no comando anterior.

```console
helm uninstall my-nginx-ingress
```

O exemplo a seguir mostra que a versão chamada *my-nginx-ingress* foi desinstalada:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Instale um aplicativo com Helm v2

### <a name="create-a-service-account"></a>Criar uma conta de serviço

Antes de implantar o Helm em um cluster AKS habilitado para RBAC, você precisa de uma conta de serviço e ligação de função para o serviço do Tiller. Para obter mais informações sobre como proteger o Helm / cluster habilitado para o Tiller em um RBAC, consulte [Tiller, Namespaces e RBAC][tiller-rbac]. Se seu cluster do AKS não for habilitado o RBAC, ignore esta etapa.

Crie um arquivo chamado `helm-rbac.yaml` e o copie no YAML a seguir:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Crie a conta de serviço e a associação de funções com o comando `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Proteger o Tiller e o Helm

O cliente do Helm e o serviço do Tiller são autenticados e se comunicam entre si usando TLS/SSL. Esse método de autenticação ajuda a proteger o cluster Kubernetes e quais serviços podem ser implantados. Para aumentar a segurança, você pode gerar seus próprios certificados autoassinados. Cada usuário do Helm receberia seu próprio certificado do cliente e o Tiller seria inicializado no cluster Kubernetes com os certificados aplicados. Para obter mais informações, consulte [Usando TLS/SSL entre o Helm e o Tiller][helm2-ssl].

Ao usar um cluster Kubernetes habilitado para RBAC, você pode controlar o nível de acesso que o Tiller tem ao cluster. Você pode definir o namespace do Kubernetes em que o Tiller é implantado e pode restringir em quais namespaces o Tiller pode implantar recursos. Essa abordagem permite criar instâncias do Tiller em namespaces diferentes, definir limites de implantação e definir o escopo dos usuários do cliente do Helm a determinados namespaces. Para obter mais informações, confira [Controles de acesso baseados em função do Helm][helm2-rbac].

### <a name="configure-helm"></a>Configurar o Helm

Para implantar um Tiller básico em um cluster do AKS, use o comando [helm init][helm2-init]. Se o cluster não está habilitado o RBAC, remova o `--service-account` argumento e o valor. Os exemplos a seguir também definem o [histórico máximo][helm2-history-max] para 200.

Se você configurou TLS/SSL para o Tiller e o Helm, ignore esta etapa de inicialização básica e, em vez disso, forneça o `--tiller-tls-` necessário conforme mostrado no exemplo a seguir.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Se você configurou TLS/SSL entre o Helm e o Tiller, forneça os parâmetros de `--tiller-tls-*` e os nomes de seus próprios certificados, conforme mostrado no exemplo a seguir:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Localizar gráficos Helm

Os gráficos do Helm são usados para implantar aplicativos em um cluster Kubernetes. Para procurar gráficos helm pré-criados, use o comando [helm search:][helm2-search]

```console
helm search
```

A saída de exemplo condensada a seguir mostra alguns dos gráficos do Helm disponíveis para uso:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Para atualizar a lista de gráficos, use o comando [helm repo update][helm2-repo-update]. O exemplo a seguir mostra uma atualização de repositório bem-sucedida:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Executar gráficos Helm

Para instalar gráficos com o Helm, use o comando [helm install][helm2-install-command] e especifique o nome do gráfico a ser instalado. Para ver a instalação de um gráfico Helm em ação, vamos instalar uma implantação básica nginx usando um gráfico Helm. Se tiver configurado TLS/SSL, adicione o parâmetro `--tls` para usar seu certificado de cliente do Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A saída do exemplo condensado a seguir mostra o status de implantação dos recursos do Kubernetes criados pelo gráfico do Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Leva um minuto ou dois para que o endereço *EXTERNAL-IP* do serviço nginx-ingress-controller seja preenchido e permita que você o acesse com um navegador da Web.

### <a name="list-helm-releases"></a>Listar versões do Helm

Para ver uma lista de versões instaladas no seu cluster, use o comando [helm list][helm2-list]. O exemplo a seguir mostra a liberação nginx-ingress implantado na etapa anterior. Se tiver configurado TLS/SSL, adicione o parâmetro `--tls` para usar seu certificado de cliente do Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Limpar os recursos

Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, use o comando `helm delete` e especifique o nome do release, conforme encontrado no comando `helm list` anterior. O exemplo a seguir exclui a versão *denominada flailing-alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar implantações de aplicativo do Kubernetes usando o Helm, consulte a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
