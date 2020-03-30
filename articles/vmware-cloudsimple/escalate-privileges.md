---
title: Solução Azure VMware by CloudSimple - Escalate CloudSimple privilégios
description: Descreve como escalar permissões cloudSimple para executar funções administrativas no Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025326"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalar privilégios cloudsimple para executar funções administrativas no Private Cloud vCenter

A abordagem de privilégios CloudSimple foi projetada para dar aos usuários do vCenter os privilégios necessários para realizar operações normais. Em alguns casos, um usuário pode exigir privilégios adicionais para executar uma tarefa específica.  Você pode escalar privilégios de um usuário vCenter SSO por um período limitado.

As razões para a escalada de privilégios podem incluir as seguintes:

* Configuração de fontes de identidade
* Gerenciamento de Usuários
* Exclusão do grupo portuário distribuído
* Instalando soluções vCenter (como aplicativos de backup)
* Criar contas de serviço

> [!WARNING]
> Ações tomadas no estado privilegiado escalonado podem afetar negativamente seu sistema e podem fazer com que seu sistema fique indisponível. Realizar apenas as ações necessárias durante o período de escalonamento.

A partir do portal CloudSimple, [aumente os privilégios](escalate-private-cloud-privileges.md) para o usuário local do CloudOwner no vCenter SSO.  Você só pode escalar o privilégio do usuário remoto se o provedor de identidade adicional estiver configurado no vCenter.  A escalada de privilégios envolve a adição do usuário selecionado ao grupo de administradores incorporados vSphere.  Apenas um usuário pode ter privilégios escalonados.  Se você precisar escalar os privilégios de outro usuário, primeiro desfaça os privilégios dos usuários atuais.

Os usuários de fontes de identidade adicionais devem ser adicionados como membros do grupo CloudOwner.

> [!CAUTION]
> Novos usuários devem ser adicionados apenas ao *Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os usuários adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas contas de serviço devem ser adicionadas ao grupo *Administradores* e as contas de serviço não devem ser usadas para fazer login na ui web vSphere.

Durante o período de escalonamento, o CloudSimple usa monitoramento automatizado com notificações de alerta associadas para identificar quaisquer alterações inadvertidas no ambiente.
