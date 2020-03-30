---
title: Interagir com contêineres do Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Saiba como executar o Azure Dev Spaces em um cluster existente com contêineres windows
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres, contêineres do Windows
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240479"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interaja com contêineres do Windows usando espaços de dev do Azure

Você pode habilitar espaços de dev do Azure em espaços de nomes Kubernetes novos e existentes. O Azure Dev Spaces será executado e os serviços de instrumentos executados em contêineres Linux. Esses serviços também podem interagir com aplicativos que são executados em contêineres windows no mesmo namespace. Este artigo mostra como usar o Dev Spaces para executar serviços em um namespace com contêineres Windows existentes. Neste momento, você não pode depurar ou anexar aos contêineres do Windows com o Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configure seu cluster

Este artigo pressupõe que você já tem um cluster com pools de nós Linux e Windows. Se você precisar criar um cluster com pools de nós Linux e Windows, você pode seguir as instruções [aqui][windows-container-cli].

Conecte-se ao cluster usando [kubectl][kubectl], o cliente da linha de comando Kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra um cluster com um nó Windows e Linux. Certifique-se de que o status está *pronto* para cada nó antes de prosseguir.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Aplique uma [mancha][using-taints] em seus nós do Windows. A mancha nos nós do Windows impede que o Dev Spaces agendaos contêineres Linux para serem executados em seus nós do Windows. O comando exemplo de comando a seguir aplica uma mancha ao nó do *Windows aksnpwin987654* do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando você aplica uma mancha a um nó, você deve configurar uma tolerância correspondente no modelo de implantação do seu serviço para executar seu serviço nesse nó. O aplicativo de exemplo já está configurado com uma [tolerância correspondente][sample-application-toleration-example] à mancha configurada no comando anterior.

## <a name="run-your-windows-service"></a>Execute seu serviço do Windows

Execute seu serviço do Windows no cluster AKS e verifique se ele está em um estado *de execução.* Este artigo usa um [aplicativo de exemplo][sample-application] para demonstrar um serviço Windows e Linux em execução em seu cluster.

Clone o aplicativo de exemplo do `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` GitHub e navegue até o diretório:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

O aplicativo de exemplo usa [o Helm 3][helm-installed] para executar o serviço windows em seu cluster. Navegue `charts` até o diretório e use helm execute o serviço Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

O comando acima usa helm para executar seu serviço windows no espaço de nome *de v.* Se você não tiver um namespace chamado *dev,* ele será criado.

Use `kubectl get pods` o comando para verificar se o serviço do Windows está sendo executado em seu cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Habilite os espaços de dev do Azure

Habilite espaços de dev no mesmo namespace que você usou para executar seu serviço windows. O comando a seguir habilita o Dev Spaces no espaço de nome *de v:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Atualize seu serviço do Windows para Espaços de Dev

Quando você habilita o Dev Spaces em um namespace existente com contêineres que já estão sendo executados, por padrão, o Dev Spaces tentará instrumentar quaisquer novos contêineres que sejam executados nesse namespace. O Dev Spaces também tentará instrumentar quaisquer novos contêineres criados para o serviço já em execução no namespace. Para evitar que o Dev Spaces instrumentalifique um contêiner em execução em seu namespace, adicione o cabeçalho *sem proxy* ao `deployment.yaml`.

Adicionar `azds.io/no-proxy: "true"` ao `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` arquivo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Use `helm list` para listar a implantação do seu serviço windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

No exemplo acima, o nome da sua implantação é *windows-service*. Atualize seu serviço do `helm upgrade`Windows com a nova configuração usando:

```cmd
helm upgrade windows-service . --namespace dev
```

Uma vez `deployment.yaml`que você atualizou o seu , Dev Spaces não tentará instrumentá-lo.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute seu aplicativo Linux com o Azure Dev Spaces

Navegue `webfrontend` até o diretório `azds prep` `azds up` e use os comandos e para executar seu aplicativo Linux em seu cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

O `azds prep --enable-ingress` comando gera o gráfico Helm e os arquivos Docker para sua aplicação.

> [!TIP]
> O [gráfico do Dockerfile e do Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para seu projeto é usado pelo Azure Dev Spaces para compilar e executar seu código, mas você poderá modificar esses arquivos se quiser, a fim de alterar a maneira como o projeto é compilado e executado.

O `azds up` comando executa seu serviço no namespace.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Você pode ver o serviço sendo executado abrindo a URL pública, que é exibida na saída do comando azds up. Neste exemplo, a URL pública é `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Navegue até o serviço em um navegador e clique em *About* na parte superior. Verifique se você vê uma mensagem do serviço *mywebapi* contendo a versão do Windows que o contêiner está usando.

![Aplicativo de exemplo mostrando a versão do Windows do mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
