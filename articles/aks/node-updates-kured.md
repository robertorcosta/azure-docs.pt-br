---
title: Atualizar e reiniciar nós Linux com kured no Azure Kubernetes Service (AKS)
description: Aprenda a atualizar nós Linux e reiniciá-los automaticamente com kured no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 74b12c1bc6e2a88582cc357c8091b5590e6bf3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191275"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Aplique atualizações de segurança e kernel aos nós Linux no Azure Kubernetes Service (AKS)

Para proteger seus clusters, as atualizações de segurança são aplicadas automaticamente aos nós Linux em AKS. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. Algumas dessas atualizações exigem uma reinicialização do nó para concluir o processo. O AKS não reinicializa automaticamente esses nós Linux para concluir o processo de atualização.

O processo para manter os nódulos do Windows Server (atualmente em pré-visualização no AKS) é um pouco diferente. Os nós do Windows Server não recebem atualizações diárias. Em vez disso, você executa uma atualização AKS que implanta novos números com a imagem e patches mais recentes do Servidor de Janelas base. Para clusters AKS que usam os álos do Windows Server, consulte [Atualizar um pool de nó sustais][nodepool-upgrade].

Este artigo mostra como usar o kured de código aberto [(KUbernetes REboot Daemon)][kured] para assistir aos nós Linux que requerem uma reinicialização e, em seguida, lidar automaticamente com o reagendamento de pods em execução e processo de reinicialização de nós.

> [!NOTE]
> `Kured` é um projeto de software livre da Weaveworks. O suporte para esse projeto no AKS é fornecido com base no melhor esforço. Um suporte adicional pode ser encontrado no canal Slack da comunidade #weave.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreender a experiência de atualização do nó AKS

Em um cluster do AKS, os nós do Kubernetes executam como VMs (máquinas virtuais) do Azure. Essas VMs baseadas em Linux usam uma imagem do Ubuntu, com o SO configurado para verificar automaticamente as atualizações todas as noites. Se atualizações de kernel ou de segurança estiverem disponíveis, elas serão baixadas e instaladas automaticamente.

![Atualização de nó do AKS e processo de reinicialização com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, exigem uma reinicialização do nó para finalizar o processo. Um nó Linux que requer uma reinicialização cria um arquivo chamado */var/run/reboot-required*. Esse processo de reinicialização não ocorre automaticamente.

Você pode usar seus próprios fluxos de trabalho e processos para manipular reinicializações de nós ou usar `kured` para orquestrar o processo. Com `kured`, um [DaemonSet][DaemonSet] é implantado que executa um pod em cada nó Linux no cluster. Esses pods no relógio DaemonSet para a existência do arquivo */var/run/reboot-required* e, em seguida, iniciam um processo para reiniciar os nós.

### <a name="node-upgrades"></a>Upgrades de nós

Há um processo adicional no AKS que permite fazer *upgrade* de um cluster. Um upgrade é basicamente passar para uma versão mais nova do Kubernetes, e não apenas aplicar atualizações de segurança do nó. Um upgrade de AKS executa as seguintes ações:

* Um novo nó é implantado com as atualizações de segurança mais recentes e a versão do Kubernetes aplicada.
* Um nó antigo é isolado e drenado.
* Os pods estão agendados no novo nó.
* O nó antigo é excluído.

Não será possível permanecer na mesma versão do Kubernetes durante um evento de upgrade. Você deverá especificar uma versão mais nova do Kubernetes. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implantar em um cluster do AKS

Para implantar `kured` o DaemonSet, instale o seguinte gráfico oficial kured Helm. Isso cria uma função e uma função de cluster, vinculações e uma `kured`conta de serviço e, em seguida, implanta o DaemonSet usando .

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Também é possível configurar parâmetros adicionais para `kured`, como integração com Prometheus ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte o [gráfico helm kured][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar nós do cluster

Por padrão, os nós do Linux no AKS verificam se há atualizações todas as noites. Se não quiser esperar, poderá executar manualmente uma atualização para verificar se `kured` executa corretamente. Primeiro, siga as etapas para [SSH para um dos nós do AKS][aks-ssh]. Depois de ter uma conexão SSH com o nó Linux, verifique se há atualizações e aplique-as da seguinte forma:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se forem aplicadas atualizações que exigem uma reinicialização do nó, um arquivo será gravado em */var/run/reboot-required*. `Kured` verifica por nós que exigem uma reinicialização a cada 60 minutos por padrão.

## <a name="monitor-and-review-reboot-process"></a>Monitorar e revisar o processo de reinicialização

Quando uma das réplicas no DaemonSet detectou que uma reinicialização do nó é necessária, um bloqueio foi colocado no nó por meio da API do Kubernetes. Esse bloqueio impedirá que pods adicionais sejam agendados no nó. O bloqueio também indica que apenas um nó deverá ser reinicializado de cada vez. Com o nó isolado, os pods em execução serão drenados do nó e o nó será reinicializado.

É possível monitorar o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes]. A seguinte saída de exemplo mostra um nó com um status *SchedulingDisabled*, na medida em que o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Quando o processo de atualização estiver concluído, você poderá exibir o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes] com o parâmetro `--output wide`. Essa saída adicional permite ver uma diferença em *KERNEL-VERSION* dos nós subjacentes, conforme mostrado na seguinte saída de exemplo. O *aks-nodepool1-28993262-0* foi atualizado em uma etapa anterior e mostra a versão do kernel *4.15.0-1039-azure*. O nó *aks-nodepool1-28993262-1* que não foi atualizado mostra a versão do kernel *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Próximas etapas

Este artigo detalhava `kured` como usar para reiniciar nós Linux automaticamente como parte do processo de atualização de segurança. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

Para clusters AKS que usam os álos do Windows Server, consulte [Atualizar um pool de nó sustais][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
