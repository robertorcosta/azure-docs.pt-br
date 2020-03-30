---
title: RDP em azure Kubernetes Service (AKS) cluster Windows Server
description: Aprenda a criar uma conexão RDP com os árdeis do Windows Server (AKS) do Azure Kubernetes Service para tarefas de solução de problemas e manutenção.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594474"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Conecte-se com os ávildes do Windows Server (AkS) do Cluster Azure Kubernetes Service (AKS) para manutenção ou solução de problemas

Durante todo o ciclo de vida do cluster Azure Kubernetes Service (AKS), talvez seja necessário acessar um nó AKS Windows Server. Esse acesso pode ser para manutenção, coleção de logs ou outras operações de solução de problemas. Você pode acessar os ádenos do AKS Windows Server usando RDP. Alternativamente, se você quiser usar o SSH para acessar os ádenos do AKS Windows Server e tiver acesso ao mesmo par de tecla que foi usado durante a criação de clusters, você pode seguir as etapas do [SSH em os álos de cluster Azure Kubernetes Service (AKS).][ssh-steps] Para fins de segurança, os nós do AKS não são expostos à internet.

O suporte ao nó do Windows Server está atualmente em visualização no AKS.

Este artigo mostra como criar uma conexão RDP com um nó AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente com um nó do Servidor Windows. Se você precisar de um cluster AKS, consulte o artigo sobre [a criação de um cluster AKS com um contêiner do Windows usando o Azure CLI][aks-windows-cli]. Você precisa do nome de usuário e senha do administrador do Windows para o nó do Servidor Windows que deseja solucionar problemas. Você também precisa de um cliente RDP, como [o Microsoft Remote Desktop][rdp-mac].

Você também precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Implantar uma máquina virtual na mesma sub-rede que seu cluster

Os nós do Windows Server do seu cluster AKS não têm endereços IP acessíveis externamente. Para fazer uma conexão RDP, você pode implantar uma máquina virtual com um endereço IP acessível ao público na mesma sub-rede que os seus álos do Windows Server.

O exemplo a seguir cria uma máquina virtual chamada *myVM* no grupo de recursos *myResourceGroup.*

Primeiro, obtenha a sub-rede usada pelo pool de nó do Windows Server. Para obter o id da sub-rede, você precisa do nome da sub-rede. Para obter o nome da sub-rede, você precisa do nome da vnet. Obtenha o nome vnet consultando seu cluster para sua lista de redes. Para consultar o cluster, você precisa de seu nome. Você pode obter tudo isso executando o seguinte no Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Agora que você tem o SUBNET_ID, execute o seguinte comando na mesma janela do Azure Cloud Shell para criar a VM:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

O exemplo a seguir, a saída mostra que a VM foi criada com sucesso e exibe o endereço IP público da máquina virtual.

```console
13.62.204.18
```

Registre o endereço IP público da máquina virtual. Você usará este endereço em uma etapa posterior.

## <a name="allow-access-to-the-virtual-machine"></a>Permitir acesso à máquina virtual

As sub-redes de pool de nós AKS são protegidas com NSGs (Network Security Groups) por padrão. Para ter acesso à máquina virtual, você terá que habilitar o acesso no NSG.

> [!NOTE]
> Os NSGs são controlados pelo serviço AKS. Qualquer mudança que fizer no NSG será substituída a qualquer momento pelo plano de controle.
>

Primeiro, obtenha o grupo de recursos e o nome nsg do nsg para adicionar a regra a:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Em seguida, crie a regra NSG:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Obtenha o endereço do nó

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Liste o endereço IP interno dos nós do Servidor Windows usando o comando [kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós no cluster, incluindo os nós do Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Grave o endereço IP interno do nó do Servidor Windows que deseja solucionar. Você usará este endereço em uma etapa posterior.

## <a name="connect-to-the-virtual-machine-and-node"></a>Conecte-se à máquina virtual e ao nó

Conecte-se ao endereço IP público da máquina virtual que você criou anteriormente usando um cliente RDP, como [o Microsoft Remote Desktop][rdp-mac].

![Imagem de conexão à máquina virtual usando um cliente RDP](media/rdp/vm-rdp.png)

Depois de conectado à sua máquina virtual, conecte-se ao *endereço IP interno* do nó do Servidor Windows que deseja solucionar usando um cliente RDP de dentro de sua máquina virtual.

![Imagem de conexão ao nó do Servidor Windows usando um cliente RDP](media/rdp/node-rdp.png)

Agora você está conectado ao nó do Servidor Windows.

![Imagem da janela cmd no nó do Servidor Windows](media/rdp/node-session.png)

Agora você pode executar quaisquer comandos de solução de problemas na janela *cmd.* Uma vez que os nós do Windows Server usam o Windows Server Core, não há uma GUI completa ou outras ferramentas de GUI quando você se conecta a um nó do Servidor Windows sobre RDP.

## <a name="remove-rdp-access"></a>Remover acesso RDP

Quando terminar, saia da conexão RDP para o nó do Servidor Windows e saia da sessão RDP para a máquina virtual. Depois de sair das duas sessões de RDP, exclua a máquina virtual com o comando [az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

E a regra do NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Próximas etapas

Se você precisar de dados adicionais de solução de problemas, você pode [visualizar os registros de nó mestre do Kubernetes][view-master-logs] ou o Monitor [Azure][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
