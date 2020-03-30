---
title: SSH nos nós de cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar uma conexão SSH com o nós de cluster do Serviço de Kubernetes do Azure (AKS) para tarefas de manutenção e solução de problemas.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593624"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Conectar com SSH aos nós de cluster do Serviço de Kubernetes do Azure (AKS) para manutenção ou solução de problemas

Em todo o ciclo de vida do seu cluster do Serviço de Kubernetes do Azure (AKS), você precisa acessar um nó do AKS. Esse acesso pode ser para manutenção, coleção de logs ou outras operações de solução de problemas. Você pode acessar os nódulos AKS usando SSH, incluindo os nós do Windows Server (atualmente em visualização no AKS). Você também pode [se conectar aos nós do Windows Server usando conexões RDP (Remote Desktop Protocol, protocolo de desktop remoto).][aks-windows-rdp] Para fins de segurança, os nós AKS não são expostos à internet. Em relação ao SSH para os nós do AKS, você deve usar o endereço IP privado.

Este artigo mostra como criar uma conexão SSH com um nó do AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Por padrão, as chaves SSH são obtidas ou geradas e adicionadas a nós quando você cria um cluster AKS. Este artigo mostra como especificar diferentes chaves SSH do que as chaves SSH usadas quando você criou seu cluster AKS. O artigo também mostra como determinar o endereço IP privado do seu nó e conectá-lo usando SSH. Se você não precisar especificar uma chave SSH diferente, então você pode pular o passo para adicionar a chave pública SSH ao nó.

Este artigo também pressupõe que você tem uma chave SSH. Você pode criar uma chave SSH usando [macOS ou Linux][ssh-nix] ou [Windows][ssh-windows]. Se você usar o PuTTY Gen para criar o par de chaves, salve o par de chaves em um formato OpenSSH em vez do formato de tecla privada PuTTy padrão (arquivo.ppk).

Você também precisa da versão 2.0.64 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Configure clusters AKS baseados em set de escala de máquina virtual para acesso ao SSH

Para configurar a escala de tela da máquina virtual baseada em set para acesso ao SSH, encontre o nome do conjunto de escala de máquina virtual do seu cluster e adicione a chave pública ssh a esse conjunto de escalas.

Use o comando [az aks show][az-aks-show] para obter o nome do grupo de recursos do seu cluster AKS e, em seguida, o comando [az vmss list][az-vmss-list] para obter o nome do seu conjunto de escalas.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

O exemplo acima atribui o nome do grupo de recursos de cluster para o *myAKSCluster* no *myResourceGroup* para *CLUSTER_RESOURCE_GROUP*. O exemplo então usa *CLUSTER_RESOURCE_GROUP* para listar o nome do conjunto de escalas e atribuí-lo a *SCALE_SET_NAME*.

> [!IMPORTANT]
> Neste momento, você só deve atualizar suas chaves SSH para seus clusters AKS baseados em set de escala de máquina virtual usando o Cli do Azure.
> 
> Para nós Linux, as chaves SSH atualmente só podem ser adicionadas usando o Cli do Azure. Se você quiser se conectar a um nó do Servidor Windows usando SSH, use as teclas SSH fornecidas quando você criou o cluster AKS e pule o próximo conjunto de comandos para adicionar sua chave pública SSH. Você ainda precisará do endereço IP do nó que deseja solucionar, que é mostrado no comando final desta seção. Alternativamente, você pode [se conectar aos nós do Windows Server usando conexões RDP (Remote Desktop Protocol, protocolo][aks-windows-rdp] de desktop remoto) em vez de usar SSH.

Para adicionar suas teclas SSH aos nós em um conjunto de escala de máquina virtual, use o [conjunto de extensão az vmss][az-vmss-extension-set] e os comandos [az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

O exemplo acima usa as variáveis *CLUSTER_RESOURCE_GROUP* e *SCALE_SET_NAME* dos comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa.pub* como o local para sua chave pública SSH.

> [!NOTE]
> Por padrão, o nome de usuário para os nós do AKS é *azureuser*.

Depois de adicionar sua chave pública SSH ao conjunto de escalas, você pode SSH em uma máquina virtual de nó nesse conjunto de escala usando seu endereço IP. Exibir os endereços IP privados dos nós de cluster AKS usando o [comando kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós do cluster, incluindo um nó do Servidor Windows.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Grave o endereço IP interno do nó que deseja solucionar.

Para acessar seu nó usando SSH, siga as etapas em [Criar a conexão SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Configure clusters AKS baseados em configuração de disponibilidade de máquina virtual para acesso ao SSH

Para configurar o cluster AKS baseado em configuração de disponibilidade de máquina virtual para acesso ao SSH, encontre o nome do nó Linux do seu cluster e adicione a chave pública SSH a esse nó.

Use o comando [az aks show][az-aks-show] para obter o nome do grupo de recursos do seu cluster AKS e, em seguida, o comando [az vm list][az-vm-list] para listar o nome da máquina virtual do nó Linux do seu cluster.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima atribui o nome do grupo de recursos de cluster para o *myAKSCluster* no *myResourceGroup* para *CLUSTER_RESOURCE_GROUP*. O exemplo então usa *CLUSTER_RESOURCE_GROUP* para listar o nome da máquina virtual. A saída de exemplo mostra o nome da máquina virtual:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Para adicionar suas chaves SSH ao nó, use o comando [az vm user update][az-vm-user-update].

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

O exemplo acima usa a variável *CLUSTER_RESOURCE_GROUP* e o nome da máquina virtual do nó de comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa.pub* como o local para sua chave pública SSH. Você também pode usar o conteúdo da sua chave pública SSH em vez de especificar um caminho.

> [!NOTE]
> Por padrão, o nome de usuário para os nós do AKS é *azureuser*.

Depois de adicionar sua chave pública SSH à máquina virtual do nó, você pode SSH naquela máquina virtual usando seu endereço IP. Exiba o endereço IP privado de um nó de cluster do AKS usando o comando [az vm list-ip-addresses][az-vm-list-ip-addresses].

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima usa a *variável CLUSTER_RESOURCE_GROUP* definida nos comandos anteriores. A saída de exemplo a seguir mostra os endereços IP privados dos nós do AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Criar a conexão SSH

Para criar uma conexão SSH para um nó do AKS, execute um pod auxiliar no cluster do AKS. Este pod auxiliar fornece acesso de SSH no cluster e depois acesso ao nó SSH adicional. Para criar e usar esse podo auxiliar, conclua as seguintes etapas:

1. Execute uma imagem de contêiner `debian` e anexe uma sessão de terminal a ela. Esse contêiner pode ser usado para criar uma sessão SSH com qualquer nó no cluster do AKS:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se você usar nós do Windows Server (atualmente em visualização no AKS), adicione um seletor de nós ao comando para agendar o contêiner Debian em um nó Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Uma vez que a sessão de terminal esteja conectada `apt-get`ao contêiner, instale um cliente SSH usando:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Abra uma nova janela de terminal, não conectada ao seu contêiner, copie sua chave SSH privada na cápsula do ajudante. Esta chave privada é usada para criar o SSH no nó AKS. 

   Se necessário, altere *~/.ssh/id_rsa* para o local de sua chave SSH privada:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Retorne à sessão de terminal para o contêiner, `id_rsa` atualize as permissões na chave SSH privada copiada para que ela seja somente leitura do usuário:

    ```console
    chmod 0600 id_rsa
    ```

1. Crie uma conexão SSH ao seu nó AKS. Novamente, o nome de usuário para os nós do AKS é *azureuser*. Aceite o prompt para continuar com a conexão quando a chave SSH for definida como confiável pela primeira vez. Você receberá o prompt de bash do seu nó do AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Remover o acesso SSH

Quando terminar, `exit` da sessão SSH e, em seguida, `exit` da sessão interativa do contêiner. Quando essa sessão do contêiner for fechada, o pod usado para acesso de SSH do cluster do AKS será excluído.

## <a name="next-steps"></a>Próximas etapas

Se precisar de dados adicionais de solução de problemas, você poderá [exibir os logs de kubelet][view-kubelet-logs] ou [exibir os logs do nó mestre de Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
