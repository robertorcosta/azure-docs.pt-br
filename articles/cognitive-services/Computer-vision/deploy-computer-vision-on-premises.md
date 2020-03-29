---
title: Use o recipiente Computer Vision com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Computer Vision em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458011"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Use o recipiente Computer Vision com Kubernetes e Helm

Uma opção para gerenciar seus contêineres de Visão computacional no local é usar Kubernetes e Helm. Usando Kubernetes e Helm para definir uma imagem de contêiner Computer Vision, criaremos um pacote Kubernetes. Este pacote será implantado em um cluster Kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados. Para obter mais informações sobre a execução de contêineres Docker sem orquestração Kubernetes, consulte [instalar e executar contêineres de Visão de Computador](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar contêineres de Visão computacional no local:

| Obrigatório | Finalidade |
|----------|---------|
| Conta do Azure | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Kubernetes CLI | O [Kubernetes CLI][kubernetes-cli] é necessário para gerenciar as credenciais compartilhadas do registro de contêineres. Kubernetes também é necessário antes da Helm, que é a gerente de pacotes Kubernetes. |
| CLI do Helm | Instale o [Helm CLI][helm-install], que é usado para instalar um gráfico de leme (definição do pacote de contêineres). |
| Recurso de Visão computacional |Para usar o contêiner, você precisará ter:<br><br>Um recurso azure **Computer Vision** e a aPI associada chave uri ponto final. Ambos os valores estão disponíveis nas páginas Visão Geral e Chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{API_KEY}**: Uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **Visão Geral**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Conecte-se ao cluster Kubernetes

Espera-se que o computador host tenha um cluster Kubernetes disponível. Veja este tutorial sobre [a implantação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceitual de como implantar um cluster Kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando credenciais do Docker com o cluster Kubernetes

Para permitir que o cluster `docker pull` Kubernetes para as `containerpreview.azurecr.io` imagens configuradas do registro de contêiner, você precisa transferir as credenciais de docker para o cluster. Execute [`kubectl create`][kubectl-create] o comando abaixo para criar um *segredo de registro de docker* com base nas credenciais fornecidas no pré-requisito de acesso ao registro de contêiner.

A partir de sua interface de linha de comando de escolha, execute o seguinte comando. Certifique-se de `<username>` `<password>`substituir `<email-address>` as credenciais de registro de contêiner e com as credenciais de registro de contêiner.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver `containerpreview.azurecr.io` acesso ao registro de contêineres, você pode criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado contra sua configuração Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir é impressa no console quando o segredo foi criado com sucesso.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi [`kubectl get`][kubectl-get] criado, `secrets` execute o com a bandeira.

```console
kubectl get secrets
```

Executando as `kubectl get secrets` impressões todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configure os valores do gráfico Helm para implantação

Comece criando uma pasta chamada *read,* depois cole o seguinte conteúdo YAML em um novo arquivo chamado *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Para configurar os valores padrão do gráfico Helm, copie e `values.yaml`cole o YAML a seguir em um arquivo chamado . Substitua `# {ENDPOINT_URI}` `# {API_KEY}` os comentários e comentários por seus próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se `billing` os `apikey` valores e valores não forem fornecidos, os serviços expirarão após 15 min. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

Crie uma pasta *de modelos* o diretório *de leitura.* Copie e cole o YAML a `deployment.yaml`seguir em um arquivo chamado . O `deployment.yaml` arquivo servirá como um modelo helm.

> Os modelos geram arquivos manifestos, que são descrições de recursos formatados pela YAML que os Kubernetes podem entender. [- Guia de modelo de gráfico de leme][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

O modelo especifica um serviço de balanceador de carga e a implantação do contêiner/imagem para Read.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de quais `containerpreview.azurecr.io` imagens docker para puxar do registro do contêiner.

> Um [gráfico Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos Kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa de aplicativos web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos helm fornecidos* puxam as imagens docker do `containerpreview.azurecr.io` Serviço de Visão computacional e o serviço correspondente do registro de contêiner.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o *gráfico de leme,* precisaremos executar o [`helm install`][helm-install-cmd] comando. Certifique-se de executar o comando `read` de instalação do diretório acima da pasta.

```console
helm install read ./read
```

Aqui está um exemplo de saída que você pode esperar ver a partir de uma execução de instalação bem-sucedida:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A implantação do Kubernetes pode levar vários minutos para ser concluída. Para confirmar se os pods e os serviços estão devidamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre a instalação de aplicativos com o Helm no Azure Kubernetes Service (AKS), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
