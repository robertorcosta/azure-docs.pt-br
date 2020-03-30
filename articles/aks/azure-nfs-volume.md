---
title: Criar um servidor Ubuntu NFS (Network File System) para uso por pods do Azure Kubernetes Service (AKS)
description: Aprenda a criar manualmente um volume de servidor Linux NFS Ubuntu para uso com pods no Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596616"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Crie e use manualmente um volume de servidor Linux NFS (Network File System) com a AKS (AKS)
O compartilhamento de dados entre contêineres é frequentemente um componente necessário de serviços e aplicativos baseados em contêineres. Você geralmente tem vários pods que precisam de acesso às mesmas informações em um volume persistente externo.    
Embora os arquivos Azure sejam uma opção, criar um Servidor NFS em uma VM Azure é outra forma de armazenamento compartilhado persistente. 

Este artigo mostrará como criar um servidor NFS em uma máquina virtual do Ubuntu. E também dar aos seus contêineres AKS acesso a este sistema de arquivos compartilhados.

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tem um Cluster AKS existente. Se você precisar de um cluster AKS, consulte o aks quickstart [usando o Azure CLI][aks-quickstart-cli] ou usando o portal [Azure][aks-quickstart-portal].

Seu Cluster AKS precisará viver nas mesmas redes virtuais ou peered que o NFS Server. O cluster deve ser criado em um VNET existente, que pode ser o mesmo VNET que sua VM.

As etapas para configurar com um VNET existente estão descritas na documentação: [criar o AKS Cluster no VNET existente][aks-virtual-network] e conectar redes virtuais com o [peering VNET][peer-virtual-networks]

Ele também assume que você criou uma Máquina Virtual Linux Ubuntu (por exemplo, 18.04 LTS). As configurações e o tamanho podem ser do seu agrado e podem ser implantados através do Azure. Para o Linux quickstart, consulte [o gerenciamento de VM do Linux][linux-create].

Se você implantar o Cluster AKS primeiro, o Azure preencherá automaticamente o campo de rede virtual ao implantar sua máquina Ubuntu, fazendo-os viver dentro do mesmo VNET. Mas se você quiser trabalhar com redes peered em vez disso, consulte a documentação acima.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Implantando o servidor NFS em uma máquina virtual
Aqui está o script para configurar um servidor NFS dentro da sua máquina virtual Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
O servidor será reiniciado (por causa do script) e você pode montar o servidor NFS para AKS.

>[!IMPORTANT]  
>Certifique-se de substituir o **AKS_SUBNET** pelo correto do seu cluster ou então "*" abrirá seu Servidor NFS para todas as portas e conexões.

Depois de criar sua VM, copie o script acima em um arquivo. Em seguida, você pode movê-lo de sua máquina local, ou onde quer que o script esteja, para o VM usando: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Uma vez que seu script esteja em sua VM, você pode entrar na VM e executá-lo através do comando:
```console
sudo ./nfs-server-setup.sh
```
Se sua execução falhar devido a um erro de permissão negado, defina a permissão de execução através do comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Conectando cluster AKS ao servidor NFS
Podemos conectar o NFS Server ao nosso cluster, provisionando uma reclamação de volume persistente e de volume persistente que especifica como acessar o volume.

É necessário conectar os dois serviços nas mesmas redes virtuais ou peered. Instruções para configurar o cluster no mesmo VNET estão aqui: [Criando cluster AKS no VNET existente][aks-virtual-network]

Uma vez que eles estejam na mesma rede virtual (ou peered), você precisa provisionar um volume persistente e uma reclamação de volume persistente em seu Cluster AKS. Os contêineres podem então montar a unidade NFS em seu diretório local.

Aqui está um exemplo de definição kubernetes para o volume persistente (esta definição assume que seu cluster e VM estão no mesmo VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Substitua **NFS_INTERNAL_IP,** **NFS_NAME** e **NFS_EXPORT_FILE_PATH** com as informações do NFS Server.

Você também precisará de um arquivo de reclamação de volume persistente. Aqui está um exemplo do que incluir:

>[!IMPORTANT]  
>**"storageClassName"** precisa permanecer uma seqüência vazia ou a reclamação não funcionará.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Solução de problemas
Se você não puder se conectar ao servidor a partir de um cluster, um problema pode ser o diretório exportado, ou seu pai, não tem permissões suficientes para acessar o servidor.

Verifique se seu diretório de exportação e seu diretório pai têm 777 permissões.

Você pode verificar permissões executando o comando abaixo e os diretórios devem ter permissões *'drwxrwwwx':*
```console
ls -l
```

## <a name="more-information"></a>Mais informações
Para obter um passo a passo completo ou para ajudá-lo a depurar sua configuração do NFS Server, aqui está um tutorial aprofundado:
  - [NFS Tutorial][nfs-tutorial]

## <a name="next-steps"></a>Próximas etapas

Para práticas recomendadas associadas, consulte [As melhores práticas para armazenamento e backups em AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
