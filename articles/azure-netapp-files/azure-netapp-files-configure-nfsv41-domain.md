---
title: Configurar o domínio padrão NFSv4.1 para arquivos do Azure NetApp | Microsoft Docs
description: Descreve como configurar o cliente NFS para usar NFSv4.1 com arquivos Do NetApp do Azure.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906280"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files

NFSv4 introduz o conceito de um domínio de autenticação. Atualmente, o Azure NetApp Files suporta mapeamento de usuários somente raiz do serviço para o cliente NFS. Para usar a funcionalidade NFSv4.1 com arquivos Do Azure NetApp, você precisa atualizar o cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento padrão do mapeamento de usuário/grupo

O mapeamento raiz `nobody` é padrão para o usuário porque `localdomain`o domínio NFSv4 está definido como . Quando você monta um volume de Arquivos DO Azure NetApp NFSv4.1 como raiz, você verá permissões de arquivo da seguinte forma:  

![Comportamento padrão do mapeamento de usuário/grupo para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como o exemplo acima mostra, `root`o usuário deve `nobody` `file1` ser , mas ele mapeia para por padrão.  Este artigo mostra como `file1` definir `root`o usuário para .  

## <a name="steps"></a>Etapas 

1. Edite `/etc/idmapd.conf` o arquivo no cliente NFS.   
    Descomentar `#Domain` a linha (ou `#` seja, remover a `localdomain` linha) e alterar o valor para `defaultv4iddomain.com`. 

    Configuração inicial: 
    
    ![Configuração inicial para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configuração atualizada:
    
    ![Configuração atualizada para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Desmonte todos os volumes NFS montados no momento.
3. Atualize `/etc/idmapd.conf` o arquivo.
4. Reinicie `rpcbind` o serviço`service rpcbind restart`no host (), ou simplesmente reinicie o host.
5. Monte os volumes NFS conforme necessário.   

    Consulte [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

O exemplo a seguir mostra a alteração de usuário/grupo resultante: 

![Configuração resultante de NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como o exemplo mostra, o usuário/grupo agora mudou de `nobody` . `root`

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento de outros usuários e grupos (não raiz)

O Azure NetApp Files suporta usuários locais (usuários criados localmente em um host) que têm permissões associadas a arquivos ou pastas em volumes NFSv4.1. No entanto, o serviço não suporta atualmente o mapeamento dos usuários/grupos em vários nós. Portanto, os usuários criados em um host não mapeiam por padrão os usuários criados em outro host. 

No exemplo a `Host1` seguir, tem três`testuser01`contas `testuser02` `testuser03`de usuário de teste existentes ( , , ): 

![Configuração resultante de NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Em `Host2`, observe que as contas de usuário de teste não foram criadas, mas o mesmo volume é montado em ambos os hosts:

![Configuração resultante de NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Próxima etapa 

[Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

