---
title: Criar dinamicamente um volume de Arquivos para vários pods no AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar dinamicamente um volume persistente com Arquivos do Azure para uso com vários pods simultâneos no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 999e106240a8a1d95c35d098062d474a0b57228d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231750"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Crie e use, dinamicamente, um volume persistente com Arquivos do Azure no AKS (Serviço de Kubernetes do Azure)

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. If multiple pods need concurrent access to the same storage volume, you can use Azure Files to connect using the [Server Message Block (SMB) protocol][smb-overview]. Este artigo mostra como criar dinamicamente um compartilhamento de Arquivos do Azure para uso por vários pods em um cluster do AKS (Serviço de Kubernetes do Azure).

For more information on Kubernetes volumes, see [Storage options for applications in AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. Execute  `az --version` para encontrar a versão. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. A storage account is automatically created in the [node resource group][node-resource-group] for use with the storage class to hold the Azure file shares. Choose of the following [Azure storage redundancy][storage-skus] for *skuName*:

* *Standard_LRS*: armazenamento com redundância local (LRS)
* *Standard_GRS*: armazenamento com redundância geográfica (GRS)
* *Standard_RAGRS*: armazenamento com redundância geográfica e acesso de leitura padrão (RA-GRS)
* *Premium_LRS* - premium locally redundant storage (LRS)

> [!NOTE]
> Azure Files support premium storage in AKS clusters that run Kubernetes 1.13 or higher.

For more information on Kubernetes storage classes for Azure Files, see [Kubernetes Storage Classes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie-o manifesto de exemplo a seguir. For more information on *mountOptions*, see the [Mount options][mount-options] section.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Create the storage class with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. The following YAML can be used to create a persistent volume claim *5 GB* in size with *ReadWriteMany* access. For more information on access modes, see the [Kubernetes persistent volume][access-modes] documentation.

Agora, crie um arquivo chamado `azure-file-pvc.yaml` e copie no YAML a seguir. Certifique-se de que o *storageClassName* corresponde à classe de armazenamento criada na última etapa:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> If using the *Premium_LRS* sku for your storage class, the minimum value for *storage* must be *100Gi*.

Create the persistent volume claim with the [kubectl apply][kubectl-apply] command:

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado. You can use the [kubectl get][kubectl-get] command to view the status of the PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

O YAML a seguir cria um pod que usa a declaração de volume persistente *azurefile* para montar o compartilhamento de arquivos do Azure no caminho */mnt/azure*. For Windows Server containers (currently in preview in AKS), specify a *mountPath* using the Windows path convention, such as *'D:'* .

Crie um arquivo chamado `azure-pvc-files.yaml` e copie no YAML a seguir. Certifique-se de que *claimName* corresponda ao PVC criado na última etapa.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Create the pod with the [kubectl apply][kubectl-apply] command.

```console
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com seu compartilhamento de arquivos do Azure montado no diretório */mnt/azure*. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

The default value for *fileMode* and *dirMode* is *0755* for Kubernetes version 1.9.1 and above. If using a cluster with Kuberetes version 1.8.5 or greater and dynamically creating the persistent volume with a storage class, mount options can be specified on the storage class object. O exemplo a seguir define *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Se usar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o *runAsUser* valor definido como *0*. For more information on Pod security context, see [Configure a Security Context][kubernetes-security-context].

## <a name="next-steps"></a>Próximos passos

For associated best practices, see [Best practices for storage and backups in AKS][operator-best-practices-storage].

Saiba mais sobre volumes persistentes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks