---
title: (PRETERIDO) Início Rápido – cluster Kubernetes do Azure para Windows
description: Aprenda a criar um cluster Kubernetes para contêineres do Windows no Serviço de Contêiner do Azure rapidamente com a CLI do Azure.
author: dlepow
ms.service: container-service
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 2e36de9f2a6af3643b6f609339d413968f6a8d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277654"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>(PRETERIDO) Implantar um cluster Kubernetes para contêineres do Windows

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar um cluster [Kubernetes](https://kubernetes.io/docs/home/) no [Serviço de Contêiner do Azure](../container-service-intro.md). Depois que o cluster é implantado, você se conecta a ele com a ferramenta de linha de comando `kubectl` Kubernetes e implanta o primeiro contêiner do Windows.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> O suporte para contêineres do Windows no Kubernetes no Serviço de Contêiner do Azure está em versão prévia. 
>

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create.](/cli/azure/group#az-group-create) Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local eastus.*

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes
Crie um cluster Kubernetes no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#az-acs-create). 

O exemplo a seguir cria um cluster denominado *myK8sCluster* com um nó Linux mestre dois nós de agente do Windows. Este exemplo cria chaves de SSH necessárias para conectar-se com o mestre do Linux. Este exemplo usa o *azureuser* como nome de usuário administrativo e *myPassword12* como a senha nos nós do Windows. Atualize esses valores para algo apropriado para seu ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Após alguns minutos, o comando é concluído e exibe informações sobre a implantação.

## <a name="install-kubectl"></a>Instalar kubectl

Para conectar-se ao cluster Kubernetes [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)a partir do seu computador cliente, use o cliente da linha de comando Kubernetes. 

Se você estiver usando o Azure CloudShell, `kubectl` já estará instalado. Se você deseja instalá-lo localmente, pode usar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

O exemplo de CLI do Azure a seguir instala `kubectl` no seu sistema. No Windows, execute este comando como administrador.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Conectar-se com kubectl

Para configurar `kubectl` e se conectar ao cluster Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). O exemplo a seguir baixa a configuração de cluster para o cluster Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a conexão ao seu cluster em seu computador, tente executar:

```azurecli-interactive
kubectl get nodes
```

`kubectl` lista os nós mestre e do agente.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Implantar um contêiner do IIS do Windows

Você pode executar um contêiner do Docker dentro de um *pod* Kubernetes, que contém um ou mais contêineres. 

Este exemplo básico usa um arquivo JSON para especificar um contêiner do Servidor de informações de internet (IIS) da Microsoft e cria o pod usando o comando `kubctl apply`. 

Crie um arquivo local chamado `iis.json` e copie o texto a seguir. Esse arquivo informa ao Kubernetes para executar o IIS no Windows Server 2016 Nano Server, usando uma imagem de contêiner pública do [Docker Hub](https://hub.docker.com/r/microsoft/iis/). O contêiner usa a porta 80, mas inicialmente é acessível somente dentro da rede de cluster.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Para iniciar o pod, digite:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Para controlar a implantação, digite:
  
```azurecli-interactive
kubectl get pods
```

Enquanto o pod está sendo implantado, o status é `ContainerCreating`. Pode levar alguns minutos para que o contêiner entre no status `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Para expor o pod ao mundo com um endereço IP público, digite o seguinte comando:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Com este comando, o Kubernetes cria um serviço e uma regra de Azure Load Balancer com um endereço IP público para o serviço. 

Execute o seguinte comando para ver o status do serviço.

```azurecli-interactive
kubectl get svc
```

Inicialmente, o endereço IP é exibido como `pending`. Depois de alguns minutos, o endereço IP externo do pod `iis` é definido:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Você pode usar um navegador da Web de sua escolha para ver a página de boas-vindas do IIS no endereço IP externo padrão:

![Imagem da navegação até o IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster Kubernetes, conectou-o a um `kubectl`e implantou um pod com um contêiner do IIS. Para saber mais sobre o Serviço de Contêiner do Azure, continue no tutorial Kubernetes.

> [!div class="nextstepaction"]
> [Gerenciar um cluster ACS Kubernetes](container-service-tutorial-kubernetes-prepare-app.md)
