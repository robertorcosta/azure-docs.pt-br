---
title: (PRETERIDO) Início Rápido – cluster Kubernetes do Azure para Linux
description: Aprenda a criar um cluster Kubernetes para contêineres do Linux no Serviço de Contêiner do Azure rapidamente com a CLI do Azure.
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 5c182d6119f59daaf21e4b4e1304363eeb0c11e5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273492"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>(PRETERIDO) Implantar um cluster Kubernetes para os contêineres do Linux

> [!TIP]
> Para a versão atualizada deste início rápido que usa o Serviço de Kubernetes do Azure, confira [Início Rápido: Implantar um cluster do AKS (Serviço de Kubernetes do Azure)](../../aks/kubernetes-walkthrough.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste início rápido, um cluster Kubernetes é implantado usando a CLI do Azure. Em seguida, um aplicativo com vários contêineres, composto por um front-end na Web e uma instância Redis, é executado no cluster. Depois de concluído, o aplicativo pode ser acessado pela internet. 

O exemplo de aplicativo usado neste documento é escrito em Python. Os conceitos e as etapas detalhadas aqui podem ser usadas para implantar uma imagem de contêiner em um cluster Kubernetes. O código, Dockerfile e arquivos de manifesto do Kubernetes criados previamente relacionados a este projeto estão disponíveis no [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este início rápido pressupõe uma compreensão básica dos conceitos de Kubernetes; para saber mais sobre Kubernetes, consulte a [documentação do Kubernetes]( https://kubernetes.io/docs/home/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. 

O seguinte exemplo cria um grupo de recursos chamado *myResourceGroup* no local *westeurope*.

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes

Crie um cluster Kubernetes no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#az-acs-create). O exemplo a seguir cria um cluster denominado *myK8sCluster* com um nó Linux mestre e três nós de agente do Linux.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

Em alguns casos, como em uma avaliação limitada, uma assinatura do Azure terá acesso limitado aos recursos do Azure. Se a implantação falhar devido à limitação nos núcleos disponíveis, reduza a contagem de agentes padrão, adicionando `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Após alguns minutos, o comando é concluído e retorna informações formatadas em json sobre o cluster. 

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster Kubernetes, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando Kubernetes. 

Se você estiver usando o Azure CloudShell, o kubectl já estará instalado. Se você deseja instalá-lo localmente, pode usar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Para configurar o kubectl e se conectar ao cluster Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Esta etapa baixa credenciais e configura a CLI de Kubernetes para usá-las.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para retornar uma lista dos nós de cluster.

```azurecli-interactive
kubectl get nodes
```

Saída:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto Kubernetes define um estado desejado para o cluster, incluindo as imagens de contêiner que devem estar em execução. Neste exemplo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo Azure Vote. 

Crie um arquivo chamado `azure-vote.yml` e copie no YAML a seguir. Se você estiver trabalhando no Azure Cloud Shell, esse arquivo poderá ser criado usando o vi ou Nano, como se estivesse trabalhando em um sistema físico ou virtual.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Use o comando [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) para executar o aplicativo.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Saída:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar o aplicativo

Conforme o aplicativo é executado, um [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) é criado para expor o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos. 

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente o **EXTERNAL -IP** para o serviço *azure-vote-front* aparece como *pendente*. Depois que o endereço EXTERNAL -IP foi alterado de *pendente* para *endereço IP*, use `CTRL-C` para interromper o processo kubectl watch. 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Agora você pode navegar para o endereço IP externo a fim de ver o aplicativo Azure Vote.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste início rápido, foram usadas imagens de contêiner criadas previamente para criar uma implantação de Kubernetes. O código de aplicativo relacionado, o Dockerfile e o arquivo de manifesto Kubernetes estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster Kubernetes e um aplicativo de com vários contêineres nele. 

Para saber mais sobre o Serviço de Contêiner do Azure e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gerenciar um cluster ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)
