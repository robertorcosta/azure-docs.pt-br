---
title: Configure as configurações de função de recurso do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como definir configurações de função de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638673"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada

Quando você configura as configurações de função de recurso do Azure, você define as configurações padrão aplicadas às atribuições de função de recurso do Azure no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Use os procedimentos a seguir para configurar o fluxo de trabalho de aprovação e especifique quem pode aprovar ou negar solicitações.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de recursos do Azure.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário na função [administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso que deseja gerenciar, como uma assinatura ou um grupo de gerenciamento.

    ![Azure recursos página listagem recursos que podem ser gerenciados](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecione **Configurações de função**.

    ![Configurações de configuração de funções listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Configuração de função definindo detalhes listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecione **Editar** para abrir o painel **De configurações** de função. A primeira guia permite atualizar a configuração para ativação de função no Gerenciamento de Identidade Privilegiada.

    ![Editar página de configurações de função com a guia Ativação aberta](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecione a guia **Atribuição** ou o botão **Seguinte: Atribuição** na parte inferior da página para abrir a guia configuração de atribuição. Essas configurações controlam as atribuições de função feitas dentro da interface de gerenciamento de identidade privilegiada.

    ![Guia Atribuição de função na página configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Use a guia **Notificação** ou o botão **Seguinte: Ativação** na parte inferior da página para chegar à guia de configuração de notificação para esta função. Essas configurações controlam todas as notificações de e-mail relacionadas a essa função.

    ![Guia Notificações de função na página configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na guia **Notificações** na página configurações da função, o Gerenciamento de Identidade Privilegiada permite o controle granular sobre quem recebe notificações e quais notificações recebem.

    - **Desatire um e-mail**<br>Você pode desativar e-mails específicos limpando a caixa de seleção do destinatário padrão e excluindo quaisquer destinatários adicionais.  

    - **Limitar e-mails a endereços de e-mail especificados**<br>Você pode desativar os e-mails enviados aos destinatários padrão limpando a caixa de seleção do destinatário padrão. Em seguida, você pode adicionar endereços de e-mail adicionais como destinatários adicionais. Se você quiser adicionar mais de um endereço de e-mail, separe-os usando um ponto e vírgula (;).

    - **Envie e-mails para destinatários padrão e destinatários adicionais**<br>Você pode enviar e-mails para destinatário padrão e destinatário adicional selecionando a caixa de seleção de destinatário padrão e adicionando endereços de e-mail para destinatários adicionais.

    - **Apenas e-mails críticos**<br>Para cada tipo de e-mail, você pode selecionar a caixa de seleção para receber apenas e-mails críticos. O que isso significa é que o Gerenciamento de Identidade Privilegiada continuará enviando e-mails para os destinatários configurados somente quando o e-mail exigir uma ação imediata. Por exemplo, e-mails pedindo aos usuários para estender sua atribuição de função não serão acionados enquanto um e-mail exigindo que os admins aprovem uma solicitação de extensão serão acionados.

1. Selecione o botão **Atualizar** a qualquer momento para atualizar as configurações da função.

## <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada e ativa) ao definir as configurações de uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Gerenciamento de Identidade Privilegiada.

Você pode escolher uma dessas opções de duração de atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Os administradores de recursos podem atribuir atribuição elegível permanente. |
| **Expirar a atribuição elegível depois** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificadas. |

E, você pode escolher uma destas opções de duração da atribuição **ativa**:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir atribuição ativa permanente. |
| **Expirar a atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificadas. |

> [!NOTE]
> Todas as atribuições que têm uma data de término especificada poderão ser renovadas por administradores de recursos. Além disso, os usuários podem iniciar solicitações de autoatendimento para [estender ou renovar as atribuições da função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

O Privileged Identity Management fornece imposição opcional da Autenticação Multifator do Azure para dois cenários diferentes.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir Autenticação Multifator na atribuição ativa

Em alguns casos, você pode querer atribuir um usuário ou grupo a uma função por uma curta duração (um dia, por exemplo). Neste caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, o Gerenciamento de Identidade Privilegiada não pode impor autenticação multifatorial quando o usuário usa sua atribuição de função porque já está ativo na função a partir do momento em que é atribuído.

Para garantir que o administrador de recursos que cumpre a atribuição seja quem eles dizem ser, você pode impor autenticação multifatorial na atribuição ativa, verificando a **Autenticação Multifatorial de exigência na caixa de atribuição ativa.**

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir a Autenticação Multifator na ativação

Você pode exigir que os usuários que são elegíveis para uma função provem quem eles estão usando a Autenticação Multifatorial do Azure antes que eles possam ser ativados. A autenticação multifatorial garante que o usuário seja quem diz ser com certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida.

Para exigir autenticação multifatorial antes da ativação, verifique a **Autenticação Multifatorial exigir na caixa de ativação.**

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificativa

Você pode exigir que os usuários insiram uma justificativa de negócios quando eles ativarem. Para exigir justificativa, marque a caixa **Exigir justificativa na atribuição ativa ** ou a caixa **Exigir justificativa na ativação**.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exige aprovação para ativar**.

1. Selecione **Selecionar aprovadores** para abrir a **página Selecionar um membro ou grupo.**

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um usuário ou grupo e clique em **Selecionar**. Você pode adicionar qualquer combinação de usuários e grupos. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as configurações de função, selecione **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
- [Configure alertas de segurança para funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-alerts.md)
