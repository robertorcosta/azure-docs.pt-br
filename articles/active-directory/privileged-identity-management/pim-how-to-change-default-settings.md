---
title: Configure as configurações de função Azure AD no PIM - Azure AD | Microsoft Docs
description: Saiba como configurar as configurações de função Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205017"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configure as configurações de função AD do Azure no Gerenciamento de Identidade Privilegiada

Um administrador de funções privilegiadapode personalizar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) em sua organização Azure Active Directory (Azure AD), incluindo a mudança da experiência para um usuário que está ativando uma atribuição de função elegível.

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções Azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções de recursos do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que esteja na função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga os passos deste artigo para aprovar ou negar pedidos de funções ad do Azure.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário na função [administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Abrir **aazure AD Privilegia a configuração** &gt; **de funções ad do Azure Azure** &gt; **.**

    ![Configurações de funções listando funções ad do Azure](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Configuração de função definindo detalhes listando várias configurações de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecione **Editar** para abrir a página Configurações da função.

    ![Editar página de configurações de função com opções para atualizar configurações de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    No painel Configurações de função de cada função, há várias configurações que podem ser configuradas.

## <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada e ativa) ao definir as configurações de uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Gerenciamento de Identidade Privilegiada.

Você pode escolher uma dessas opções de duração de atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Os admins globais e os admins de função privilegiado podem atribuir atribuição elegível permanente. |
| **Expirar a atribuição elegível depois** | Os admins globais e os admins de função privilegiado podem exigir que todas as atribuições elegíveis tenham uma data de início e término especificadas. |

E, você pode escolher uma destas opções de duração da atribuição **ativa**:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os admins globais e os admins de função privilegiado podem atribuir atribuição ativa permanente. |
| **Expirar a atribuição ativa após** | Os admins globais e os admins de função privilegiado podem exigir que todas as atribuições ativas tenham uma data de início e término especificada. |

> [!NOTE]
> Todas as atribuições que tenham uma data de término especificada podem ser renovadas por admins Global e admins de função Privileged. Além disso, os usuários podem iniciar solicitações de autoatendimento para [estender ou renovar as atribuições da função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

O Privileged Identity Management fornece imposição opcional da Autenticação Multifator do Azure para dois cenários diferentes.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir Autenticação Multifator na atribuição ativa

Em alguns casos, você pode querer atribuir um usuário a uma função por uma curta duração (um dia, por exemplo). Neste caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, o Gerenciamento de Identidade Privilegiada não pode impor autenticação multifatorial quando o usuário usa sua atribuição de função porque já está ativo na função a partir do momento em que é atribuído.

Para garantir que o administrador que cumpra a atribuição seja quem eles dizem ser, você pode impor autenticação multifatorial na atribuição ativa, verificando a **Autenticação Multifatorial de exigência na caixa de atribuição ativa.**

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir a Autenticação Multifator na ativação

Você pode exigir que os usuários que são elegíveis para uma função provem quem eles estão usando a Autenticação Multifatorial do Azure antes que eles possam ser ativados. A autenticação multifatorial garante que o usuário seja quem diz ser com certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida.

Para exigir autenticação de vários fatores antes da ativação, verifique a **Autenticação multifatorial exigir na** caixa de ativação na guia **'Editar configuração de função**' '

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificativa

Você pode exigir que os usuários insiram uma justificativa de negócios quando eles ativarem. Para exigir justificativa, marque a caixa **Exigir justificativa na atribuição ativa ** ou a caixa **Exigir justificativa na ativação**.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se definir vários aprovadores, a aprovação é concluída assim que um deles aprovar ou negar. Você não pode exigir a aprovação de pelo menos dois usuários. Para exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exige aprovação para ativar**.

1. Selecione **Os aprovadores de seleção**.

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um usuário e clique em **Selecionar**. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as configurações de função, selecione **Atualizar** para salvar suas alterações.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **Configurações**.

    ![Funções Azure AD - Configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **funções**.

1. Selecione a função cujas configurações você deseja configurar.

    ![Funções Azure AD - Configurações Funções](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de configurações de cada função, há diversas configurações que você pode realizar. Essas configurações afetam apenas os usuários que são atribuições **elegíveis**, mas não atribuições **permanentes**.

## <a name="activations"></a>Ativações

Use o controle deslizante **Ativações** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Isso pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Use as **notificações** para especificar se os administradores receberão notificações por email quando as funções estiverem ativadas. Esta notificação pode ser útil para detectar ativações não autorizadas ou ilegítimas.

Quando definido como **Habilitar**, as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para obter mais informações, consulte [notificações de e-mail em Gerenciamento de Identidade Privilegiado](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de Incidente/Solicitação

Use o interruptor **de bilhete Incidente/Solicitação** para exigir que os administradores elegíveis incluam um número de bilhete quando ativarem sua função. Essa prática pode tornar as auditorias de acesso ao papel mais eficazes.

## <a name="multi-factor-authentication"></a>Autenticação Multifator

Use a opção **Autenticação Multifator** para especificar se os usuários deverão confirmar a identidade com MFA antes de poderem ativar as funções. Eles só têm que verificar sua identidade uma vez por sessão, não toda vez que ativam uma função. Há duas dicas para ter em mente quando você habilita a MFA:

- Os usuários que possuem contas microsoft @outlook.compara seus endereços de e-mail (normalmente , mas nem sempre) não podem se registrar para autenticação multifatorial do Azure. Se você quiser atribuir funções a usuários com contas Microsoft, você deve torná-los administradores permanentes ou desativar a autenticação multifatorial para essa função.
- Não é possível desativar a autenticação multifatorial do Azure para funções altamente privilegiadas para o Azure AD e o Office 365. Este recurso de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador de Proteção de Informações do Azure
  - Administrador de cobrança
  - Administrador de aplicativos de nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador da Dinâmica 365
  - Aprovador de acesso do Sistema de Proteção de Dados do Cliente
  - Gravadores de diretório
  - Administrador do Exchange
  - Administrador global
  - Administrador do Intune
  - Administrador de BI de energia
  - Administrador de função com privilégios
  - Administrador de segurança
  - Administrador do SharePoint
  - Administrador do Skype for Business
  - Administrador de usuários

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Defina a opção **Requer aprovação** para **Habilitado**. O painel expande com opções para selecionar aprovadores.

    ![Funções Azure AD - Configurações - Exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador de funções Privilegiadas se torna o aprovador padrão e, em seguida, é obrigado a aprovar todas as solicitações de ativação para esta função.

1. Para adicionar aprovadores, clique **em Selecionar aprovadores**.

    ![Funções Azure AD - Configurações - Exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais aprovadores, além do administrador de funções Privilegiadoe e clique em **Selecionar**. Recomendamos que você adicione pelo menos dois aprovadores. Mesmo se você se somar como um aprovador, você não pode auto-aprovar uma ativação de função. Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as configurações de função, **selecione Salvar** para salvar suas alterações.

---

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
- [Configure alertas de segurança para funções azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-configure-security-alerts.md)
