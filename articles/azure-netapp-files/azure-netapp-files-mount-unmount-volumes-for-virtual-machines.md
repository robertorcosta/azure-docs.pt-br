---
title: Montar Azure NetApp Files volumes para máquinas virtuais
description: Saiba como montar ou desmontar um volume para máquinas virtuais do Windows ou máquinas virtuais do Linux no Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: f9dc54959979d00d57536e3a3fa2262d27e28f96
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072189"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux 

Você pode montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux conforme necessário.  As instruções de montagem para máquinas virtuais do Linux estão disponíveis no Azure NetApp Files.  

> [!IMPORTANT] 
> Você deve ter pelo menos uma política de exportação para poder acessar um volume do NFS.

1. Clique na folha **volumes** e selecione o volume para o qual você deseja montar. 
2. Clique em **instruções de montagem** do volume selecionado e siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Se você estiver montando um volume NFS, certifique-se de usar a `vers` opção no `mount` comando para especificar a versão do protocolo NFS que corresponde ao volume que você deseja montar. 
    * Se você estiver usando o NFSv 4.1, use o seguinte comando para montar o sistema de arquivos:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Se você usar o NFSv 4.1, verifique se todas as VMs montando a exportação usam nomes de host exclusivos.

3. Se você quiser que um volume NFS seja montado automaticamente quando uma VM do Azure for iniciada ou reiniciada, adicione uma entrada ao `/etc/fstab` arquivo no host. 

    Por exemplo: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` é o endereço IP do volume de Azure NetApp Files encontrado na folha de propriedades do volume.
    * `$FILEPATH` é o caminho de exportação do volume de Azure NetApp Files.
    * `$MOUNTPOINT` é o diretório criado no host do Linux usado para montar a exportação do NFS.

4. Se você quiser montar o volume para o Windows usando NFS:

    a. Monte o volume em uma VM UNIX ou Linux primeiro.  
    b. Execute um `chmod 777` `chmod 775` comando ou no volume.  
    c. Monte o volume por meio do cliente NFS no Windows.
    
5. Se você quiser montar um volume Kerberos do NFS, consulte [Configurar a criptografia do nfsv 4.1](configure-kerberos-encryption.md) para obter detalhes adicionais. 

## <a name="next-steps"></a>Próximas etapas

* [Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes sobre NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Visão geral do Network File System](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
* [Montar um volume Kerberos do NFS](configure-kerberos-encryption.md#kerberos_mount)
