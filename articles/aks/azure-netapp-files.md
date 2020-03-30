---
title: Integre arquivos do Azure NetApp com o Serviço Azure Kubernetes
description: Saiba como integrar arquivos do Azure NetApp com o Azure Kubernetes Service
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273744"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integre arquivos do Azure NetApp com o Serviço Azure Kubernetes

[O Azure NetApp Files][anf] é um serviço de armazenamento de arquivos medido de classe empresarial, de alto desempenho, em execução no Azure. Este artigo mostra como integrar arquivos do Azure NetApp com o Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Antes de começar
Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Seu cluster AKS também deve estar [em uma região que suporta arquivos Do Azure NetApp][anf-regions].

Você também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você usa arquivos do Azure NetApp:

* O Azure NetApp Files só está disponível [em regiões selecionadas do Azure][anf-regions].
* Antes de usar o Azure NetApp Files, você deve ter acesso ao serviço Azure NetApp Files. Para solicitar o acesso, você pode usar o formulário de envio da [lista de espera do Azure NetApp Files][anf-waitlist]. Você não pode acessar o serviço Azure NetApp Files até receber o e-mail oficial de confirmação da equipe de Arquivos do Azure NetApp.
* O serviço Azure NetApp Files deve ser criado na mesma rede virtual do seu cluster AKS.
* Após a implantação inicial de um cluster AKS, apenas o provisionamento estático para arquivos Do Azure NetApp é suportado.
* Para usar o provisionamento dinâmico com arquivos do Azure NetApp, instale e configure a versão 19.07 do [NetApp Trident](https://netapp-trident.readthedocs.io/) ou posterior.

## <a name="configure-azure-netapp-files"></a>Configure arquivos do Azure NetApp

> [!IMPORTANT]
> Antes de registrar o provedor de recursos *Microsoft.NetApp,* você deve preencher o formulário de envio da [lista de espera do Azure NetApp Files][anf-waitlist] para sua assinatura. Você não pode registrar o recurso fornecido até receber o e-mail oficial de confirmação da equipe de Arquivos do Azure NetApp.

Registre o provedor de recursos *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Elas podem levar algum tempo para ser concluída.

Quando você cria uma conta do Azure NetApp para uso com AKS, você precisa criar a conta no grupo de recursos do **nó.** Primeiro, obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o parâmetro de consulta `--query nodeResourceGroup`. O exemplo a seguir obtém o grupo de recursos de nó para o cluster AKS chamado *myAKSCluster* no nome de grupo de recursos *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Crie uma conta azure NetApp Files no grupo de recursos de **nó** e na mesma região que seu cluster AKS usando [a az netappfiles conta criar][az-netappfiles-account-create]. O exemplo a seguir cria uma conta chamada *myaccount1* no *grupo de* recursos MC_myResourceGroup_myAKSCluster_eastus e região *de Eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Crie um novo pool de capacidade usando [a criação do pool az netappfiles][az-netappfiles-pool-create]. O exemplo a seguir cria um novo pool de capacidade chamado *mypool1* com 4 TB de tamanho e nível de serviço *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Crie uma sub-rede [para delegar aos Arquivos Do Azure NetApp][anf-delegate-subnet] usando [a rede vnet az criar][az-network-vnet-subnet-create]. *Esta sub-rede deve estar na mesma rede virtual que seu cluster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Crie um volume usando [a criação de volume az netappfiles][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Criar o PersistentVolume

Liste os detalhes do seu volume usando [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Crie `pv-nfs.yaml` um Volume Persistente. Substitua-o `path` pelo `server` *creationToken* e por *ipAddress* do comando anterior. Por exemplo: 

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Atualize o *servidor* e o *caminho* para os valores do volume de NFS (Network File System) que você criou na etapa anterior. Crie o PersistentVolume com o comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Verifique se o *status* do PersistentVolume está *disponível* usando o comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Criar o PersistentVolumeClaim

Crie `pvc-nfs.yaml` um Volume Persistente. Por exemplo: 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Crie o PersistentVolumeClaim com o comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Verifique se o *status* do PersistentVolumeClaim está *vinculado* usando o comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Monte com uma cápsula

Crie `nginx-nfs.yaml` um pod definidor que usa o PersistentVolumeClaim. Por exemplo: 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Crie o pod com o comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Verifique se o pod está *em execução* usando o comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Verifique se o volume foi montado no pod usando [o executivo kubectl][kubectl-exec] para se conectar à cápsula e, em seguida, `df -h` verificar se o volume está montado.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure NetApp, consulte [O que é o Azure NetApp Files][anf]. Para obter mais informações sobre o uso de NFS com AKS, consulte [Criar e usar manualmente um volume de servidor Linux NFS (Network File System) com o Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
