---
title: (PRETERIDO) Tutorial do Serviço de Contêiner do Azure – atualizar aplicativo
description: Tutorial do Serviço de Contêiner do Azure – atualizar aplicativo
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e65ca30e4f15b6f69f39160c67813047c40ce8ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274122"
---
# <a name="deprecated-update-an-application-in-kubernetes"></a>(PRETERIDO) Atualizar um aplicativo no Kubernetes

> [!TIP]
> Para a versão atualizada deste tutorial que usa o Serviço de Kubernetes do Azure, confira [Tutorial: Atualizar aplicativo no AKS (Serviço de Kubernetes do Azure)](../../aks/tutorial-kubernetes-app-update.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Depois que um aplicativo foi implantado no Kubernetes, ele pode ser atualizado especificando uma nova imagem de contêiner ou versão de imagem. Ao fazer isso, a atualização é dividida em etapas para que apenas uma parte da implantação seja atualizada simultaneamente. Essa atualização em etapas permite que o aplicativo continue em execução durante a atualização. Ela também oferece um mecanismo de reversão, caso ocorra uma falha de implantação. 

Neste tutorial, parte seis de sete, o aplicativo de exemplo Azure Vote é atualizado. As tarefas a serem concluídas incluem:

> [!div class="checklist"]
> * Atualizando o código do aplicativo front-end
> * Criando uma imagem de contêiner atualizada
> * Enviando a imagem de contêiner por push para o Registro de Contêiner do Azure
> * Implantando a imagem de contêiner atualizada

Nos tutoriais subsequentes, o Log Analytics é configurado para monitorar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, a imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. Em seguida, o aplicativo foi executado no cluster Kubernetes. 

Um repositório de aplicativo também foi clonado, o qual inclui o código-fonte do aplicativo e um arquivo do Docker Compose pré-criado usado neste tutorial. Verifique se você criou um clone do repositório e se você alterou os diretórios para o diretório clonado. Dentro está um diretório chamado `azure-vote` e um arquivo chamado `docker-compose.yml`.

Se você ainda não completou essas etapas e deseja continuar acompanhando, retorne para [Tutorial 1 – Criar mensagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Atualizar aplicativo

Para este tutorial, é feita uma alteração no aplicativo e o aplicativo atualizado é implantado no cluster Kubernetes. 

O código-fonte do aplicativo pode ser encontrado no diretório `azure-vote`. Abra o arquivo `config_file.cfg` com qualquer editor de texto ou de código. Neste exemplo, `vi` é usado.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Altere os valores de `VOTE1VALUE` e `VOTE2VALUE` e, em seguida, salve o arquivo.

```plaintext
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Salve e feche o arquivo.

## <a name="update-container-image"></a>Atualizar imagem de contêiner

Utilize [docker-compose](https://docs.docker.com/compose/) para recriar a imagem de front-end e executar o aplicativo atualizado. O argumento `--build` é usado para instruir o Docker Compose a recriar a imagem do aplicativo.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testar o aplicativo localmente

Navegue até `http://localhost:8080` para ver o aplicativo atualizado.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Marcar e enviar mensagens por push

Marque a imagem `azure-vote-front` com o loginServer do registro de contêiner. 

Obter o nome do servidor de logon com o comando [az acr list](/cli/azure/acr#az-acr-list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize a [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para marcar a imagem. Substitua `<acrLoginServer>` pelo nome do servidor de logon do Registro de Contêiner do Azure ou pelo nome do host do registro público. Note também que a versão da imagem é atualizada para `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Utilize [docker push](https://docs.docker.com/engine/reference/commandline/push/) para carregar a imagem no seu registro. Substitua `<acrLoginServer>` pelo nome do servidor de logon do Registro de Contêiner do Azure.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Implantar aplicativo de atualização

Para garantir o tempo de atividade máximo, várias instâncias do pod de aplicativos devem estar em execução. Verifique essa configuração com o comando [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```bash
kubectl get pod
```

Saída:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se não houver vários pods executando a imagem do azure-vote-front, dimensione a implantação do `azure-vote-front`.


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

Para atualizar o aplicativo, utilize o comando [kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set). Atualize `<acrLoginServer>` com o servidor de logon ou o nome do host do registro de contêiner.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Para monitorar a implantação, use o comando [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get). Conforme o aplicativo atualizado é implantado, os pods são encerrados e recriados com a nova imagem de contêiner.

```bash
kubectl get pod
```

Saída:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testar aplicativo atualizado

Obtenha o endereço IP externo do serviço `azure-vote-front`.

```bash
kubectl get service azure-vote-front
```

Navegue até o endereço IP para ver o aplicativo atualizado.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atualizou um aplicativo e distribuiu essa atualização para um cluster Kubernetes. Concluímos as seguintes tarefas:

> [!div class="checklist"]
> * Atualizamos o código do aplicativo front-end
> * Criamos uma imagem de contêiner atualizada
> * Enviamos a imagem de contêiner por push para o Registro de Contêiner do Azure
> * Implantamos o aplicativo atualizado

Avance para o próximo tutorial para saber mais sobre como monitorar o Kubernetes com o Log Analytics.

> [!div class="nextstepaction"]
> [Monitorar Kubernetes com o Log Analytics](./container-service-tutorial-kubernetes-monitor.md)
