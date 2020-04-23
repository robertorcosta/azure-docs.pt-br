---
title: Monte azure NetApp Arquivos volumes para máquinas virtuais
description: Aprenda a montar ou desmontar um volume para máquinas virtuais Windows ou máquinas virtuais Linux no Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084933"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux 

Você pode montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux conforme necessário.  As instruções de montagem para máquinas virtuais do Linux estão disponíveis no Azure NetApp Files.  

1. Clique na **lâmina Volumes** e selecione o volume para o qual deseja montar. 
2. Clique **em 'Montar as instruções'** do volume selecionado e siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se estiver usando o NFSv4.1, use o seguinte comando para montar o sistema de arquivos:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se você quiser ter um volume NFS montado automaticamente quando uma VM do Azure `/etc/fstab` for iniciada ou reiniciada, adicione uma entrada ao arquivo no host. 

    Por exemplo: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`é o endereço IP do volume de Arquivos do Azure NetApp encontrado na lâmina de propriedades de volume.
    * `$FILEPATH`é o caminho de exportação do volume de Arquivos Do Azure NetApp.
    * `$MOUNTPOINT`é o diretório criado no host Linux usado para montar a exportação nfs.

4. Se você quiser montar o volume no Windows usando NFS:

    a. Monte o volume em um Unix ou Linux VM primeiro.  
    b. Execute `chmod 777` um `chmod 775` ou comando contra o volume.  
    c. Monte o volume através do cliente NFS no Windows.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes da NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Visão geral do Network File System](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
