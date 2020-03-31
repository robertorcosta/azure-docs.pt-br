---
title: Conceder acesso para gerenciar o PIM - Azure Active Directory | Microsoft Docs
description: Saiba como permitir acesso a outros administradores para gerenciar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022123"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Conceder acesso a outros administradores para gerenciar o Gerenciamento de Identidade Privilegiada

O administrador global que permite o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) para uma organização recebe automaticamente atribuições de funções e acesso ao Gerenciamento de Identidade Privilegiada. No entanto, ninguém mais em sua organização Azure Active Directory (Azure AD) obtém acesso de gravação por padrão, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso somente à leitura ao Gerenciamento de Identidade Privilegiada. Para conceder acesso ao Gerenciamento de Identidade Privilegiada, o primeiro usuário pode atribuir outros à função **administrador de funções privilegiadas.**

> [!NOTE]
> O gerenciamento de identidade privilegiada requer a autenticação multifatorial do Azure. Como as contas da Microsoft não podem se registrar para autenticação multifatorial do Azure, um usuário que faz logon com uma conta Microsoft não pode acessar o Gerenciamento de Identidade Privilegiada.

Certifique-se de que haja sempre pelo menos dois usuários em uma função de Administrador com Função com Privilégios, no caso de um usuário ser bloqueado ou a conta ser excluída.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerenciar PIM

1. Faça login no [portal Azure](https://portal.azure.com/).

1. No Azure AD, abra **o Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **funções**.

    ![Funções ad de gerenciamento de identidade privilegiadas - Funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecione a função **administradora de funções privilegiadas** para abrir a página membros.

    ![Administrador com Função com Privilégios - Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecione **Adicionar membro** para abrir o painel Adicionar membros gerenciados.

1. Selecione **Selecionar membros** para abrir o painel Selecionar membros.

    ![Administrador com Função com Privilégios - Selecionar membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e clique em **Selecionar**.

1. Selecione **OK** para tornar o membro elegível para a função **administrador a função privilegiada.**

    Quando você atribui uma nova função a alguém em Gerenciamento de Identidade Privilegiada, ele é configurado automaticamente como **Elegível** para ativar a função.

1. Para tornar o membro permanente, selecione o usuário na lista de membros do Administrador de Funções Privilegiadas.

1. Selecione **Mais** e, em seguida, **torne permanente** para tornar a atribuição permanente.

    ![Administrador com Função com Privilégios - Tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envie ao usuário um link para [iniciar o uso do Gerenciamento de Identidade Privilegiada](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover acesso para gerenciar PIM

Antes de remover alguém da função Administrador com Função com Privilégios, sempre verifique se ainda haverá pelo menos dois usuários atribuídos a ela.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **funções**.

1. Selecione a função **administradora de funções privilegiadas** para abrir a página membros.

1. Selecione a caixa de seleção ao lado do usuário que deseja remover e, em seguida, **selecione Remover membro**.

    ![Administrador com Função com Privilégios - Remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando você for solicitado a confirmar que deseja remover o membro da função, selecione **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Começar usando o Privileged Identity Management](pim-getting-started.md)
