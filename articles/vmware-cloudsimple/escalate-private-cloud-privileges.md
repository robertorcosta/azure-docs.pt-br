---
title: Aumentar privilégios de nuvem privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como escalar privilégios em sua nuvem privada para funções administrativas no vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870468"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalar privilégios do Private Cloud vCenter a partir do portal CloudSimple

Para acesso administrativo ao seu vCenter private cloud, você pode escalar temporariamente seus privilégios cloudsimple.  Usando privilégios elevados, você pode instalar soluções VMware, adicionar fontes de identidade e gerenciar usuários.

Novos usuários podem ser criados no domínio vCenter SSO e ter acesso ao vCenter.  Quando você criar novos usuários, adicione-os aos grupos incorporados CloudSimple para acessar o vCenter.  Para obter mais informações, consulte [o modelo de permissão CloudSimple Private Cloud do VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Não faça alterações de configuração para componentes de gerenciamento. Ações tomadas durante o estado privilegiado escalonado podem afetar negativamente seu sistema ou podem fazer com que seu sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalonar privilégios

1. Acesse o [portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos,** selecione a Nuvem Privada para a qual você deseja escalar privilégios.

3. Perto da parte inferior da página Resumo em **Alterar privilégios vSphere**, clique **em Escalar**.

    ![Alterar o privilégio vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de usuário vSphere.  Apenas `CloudOwner@cloudsimple.local` o usuário local pode ser escalado.

5. Selecione o intervalo de tempo de escala ção a partir da queda. Escolha o menor período que lhe permitirá concluir a tarefa.

6. Selecione a caixa de seleção para confirmar se você entende os riscos.

    ![Escalar diálogo de privilégios](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalada pode levar alguns minutos. Quando concluído, clique em **OK**.

A escalação do privilégio começa e dura até o final do intervalo selecionado.  Você pode fazer login no seu vCenter em nuvem privado para fazer tarefas administrativas.

> [!IMPORTANT]
> Apenas um usuário pode ter privilégios escalonados.  Você deve desescalar os privilégios do usuário antes de aumentar os privilégios de outro usuário.

> [!CAUTION]
> Novos usuários devem ser adicionados apenas ao *Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os usuários adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas contas de serviço devem ser adicionadas ao grupo *Administradores* e as contas de serviço não devem ser usadas para fazer login na ui web vSphere.

## <a name="extend-privilege-escalation"></a>Estender a escalada de privilégios

Se você precisar de tempo adicional para concluir suas tarefas, você pode estender o período de escalonamento de privilégios.  Escolha o intervalo de tempo adicional de escalaque permite concluir as tarefas administrativas.

1. No **portal Nuvens** > **Privadas** de Recursos no portal CloudSimple, selecione a Nuvem Privada para a qual você deseja estender a escalação de privilégios.

2. Perto da parte inferior da guia Resumo, clique em **Estender o escalonamento de privilégios**.

    ![Estender a escalada de privilégios](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo de escalação a partir da queda. Reveja o novo tempo de término da escalada.

4. Clique **em Salvar** para estender o intervalo.

## <a name="de-escalate-privileges"></a>Desescalar privilégios

Uma vez que suas tarefas administrativas estejam concluídas, você deve desescalar seus privilégios.  

1. No **portal Nuvens** > **Privadas** de Recursos no portal CloudSimple, selecione a Nuvem Privada para a qual você deseja desescalar privilégios.

2. Clique em **De-escala**.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar erros, saia do vCenter e faça login novamente após privilégios de desescalada.

## <a name="next-steps"></a>Próximas etapas

* [Configure as fontes de identidade do vCenter para usar o Active Directory](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Instale a solução de backup para [máquinas virtuais de carga de trabalho de backup](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/)