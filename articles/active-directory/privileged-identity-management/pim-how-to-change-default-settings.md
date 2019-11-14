---
title: Definir as configurações de função do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como definir as configurações de função do Azure AD no Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021808"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Definir as configurações de função do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode personalizar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Configurações**.

    ![Funções do Azure AD – configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Clique em **Funções**.

1. Clique na função cujas configurações você deseja configurar.

    ![Funções do Azure AD – funções de configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de configurações de cada função, há diversas configurações que você pode realizar. Essas configurações afetam apenas os usuários que são atribuições **elegíveis**, mas não atribuições **permanentes**.

## <a name="activations"></a>Ativações

Use o controle deslizante **Ativações** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Isso pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Use as **notificações** para especificar se os administradores receberão notificações por email quando as funções estiverem ativadas. Essa notificação pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido como **Habilitar**, as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para obter mais informações, consulte [notificações por email em Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de Incidente/Solicitação

Use a opção de **tíquete de incidente/solicitação** para exigir que os administradores qualificados incluam um número de tíquete quando ativarem sua função. Essa prática pode tornar as auditorias de acesso de função mais eficientes.

## <a name="multi-factor-authentication"></a>Autenticação Multifator

Use a opção **Autenticação Multifator** para especificar se os usuários deverão confirmar a identidade com MFA antes de poderem ativar as funções. Eles só precisam verificar sua identidade uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente quando você habilita a MFA:

- Os usuários que têm contas da Microsoft para seus endereços de email (normalmente @outlook.com, mas nem sempre) não podem se registrar para a autenticação multifator do Azure. Se você quiser atribuir funções a usuários com contas da Microsoft, você deve torná-los administradores permanentes ou desabilitar a autenticação multifator para essa função.
- Não é possível desabilitar a autenticação multifator do Azure para funções altamente privilegiadas para o Azure AD e o Office 365. Esse recurso de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador da proteção de informações do Azure
  - Administrador de cobrança
  - Administrador de aplicativos de nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador do Dynamics 365
  - Aprovador de acesso do Sistema de Proteção de Dados do Cliente
  - Gravadores de diretório
  - Administrador do Exchange
  - Administrador global
  - Administrador do Intune
  - Administrador de Power BI
  - Administrador de função com privilégios
  - Administrador de segurança
  - Administrador do SharePoint
  - Administrador do Skype for Business
  - Administrador de usuários

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Defina a opção **Requer aprovação** para **Habilitado**. O painel expande com opções para selecionar aprovadores.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador da função com privilégios se tornará o aprovador padrão e, em seguida, será necessário para aprovar todas as solicitações de ativação para essa função.

1. Para especificar aprovadores, clique em **Selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais Aprovadores, além do administrador de função com privilégios e, em seguida, clique em **selecionar**. Você pode selecionar usuários ou grupos. Recomendamos pelo menos dois aprovadores. Mesmo que você se adicione como um aprovador, não é possível aprovar automaticamente uma ativação de função. Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **salvar** para salvar as alterações.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management](pim-how-to-configure-security-alerts.md)
