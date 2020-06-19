---
title: Como gerenciar senhas de aplicativos – Azure Active Directory | Microsoft Docs
description: Saiba mais sobre senhas do aplicativo e para que elas são usadas com relação à verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 8c6a9304927f5d4bcad895b725955c522b60207a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266229"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerenciar senhas de aplicativos para verificação em duas etapas

>[!Important]
>O administrador pode não permitir que você use senhas de aplicativo. Caso você não veja a opção **Senhas de aplicativos**, elas não estão disponíveis em sua organização.

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas de aplicativos são geradas automaticamente e devem ser inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, depois que a verificação em duas etapas estiver ativada, você não precisará mais de senhas de aplicativos para clientes do Office 2013. Para obter mais informações, consulte o artigo [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicativos

Durante o processo de registro inicial da verificação de dois fatores, você receberá uma única senha de aplicativo. Se você precisar de mais de uma, será necessário criá-las por conta própria. Você pode criar senhas de aplicativo de várias áreas, dependendo de como a verificação de dois fatores é configurada em sua organização. Para obter mais informações sobre como registrar-se para usar a verificação de dois fatores com sua conta corporativa ou de estudante, confira [Visão geral da verificação de dois fatores e sua conta corporativa ou de estudante](multi-factor-authentication-end-user-first-time.md) e os artigos relacionados.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e excluir suas senhas de aplicativo

Você pode criar e excluir senhas de aplicativo com base em como você usa a verificação de dois fatores:

- **Sua organização usa a verificação de dois fatores e a página Verificação de segurança adicional.** Se você estiver usando sua conta corporativa ou de estudante (como alain@contoso.com) com verificação de dois fatores em sua organização, você poderá gerenciar suas senhas de aplicativo na [página Verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx). Para obter instruções detalhadas, confira [Criar e excluir senhas de aplicativo usando a página Verificação de segurança adicional](#create-and-delete-app-passwords-from-the-additional-security-verification-page) neste artigo.

- **Sua organização usa a verificação de dois fatores e o portal do Office 365.** Se você estiver usando sua conta corporativa ou de estudante (como alain@contoso.com), a verificação de dois fatores e aplicativos do Office 365 em sua organização, poderá gerenciar suas senhas de aplicativo na [Página do portal do Office 365](https://www.office.com). Para obter instruções detalhadas, confira [Criar e excluir senhas de aplicativo usando o portal do Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) neste artigo.

- **Você está usando a verificação de dois fatores com um conta Microsoft pessoal.** Se você estiver usando uma conta Microsoft pessoal (como alain@outlook.com) com verificação de dois fatores, poderá gerenciar suas senhas de aplicativo na [página de noções básicas de segurança](https://account.microsoft.com/security/). Para obter instruções detalhadas, confira [Usar senhas de aplicativo com aplicativos que não dão suporte à verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Criar e excluir senhas de aplicativo na página Verificação de segurança adicional

Você pode criar e excluir senhas de aplicativo na página **Verificação de segurança adicional** para sua conta corporativa ou de estudante.

1. Entre na [página Verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx) e selecione **Senhas de aplicativo**.

    ![Página Senhas do aplicativo, com a guia Senhas do aplicativo realçada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selecione **Criar**, digite o nome do aplicativo que requer a senha do aplicativo e selecione **Avançar**.

    ![Página Criar senhas do aplicativo, com o nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copie a senha da página **Sua senha do aplicativo** e selecione **Fechar**.

    ![Sua página de senha do aplicativo com a senha para seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na página **Senhas do aplicativo**, verifique se seu aplicativo está listado.

     ![Página de senhas do aplicativo, com o novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Abra o aplicativo para o qual você criou a senha do aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve fazer isso uma vez por aplicativo.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Para excluir uma senha de aplicativo usando a página Senhas de aplicativo

1. Na página **Senhas do aplicativo** e, em seguida, selecione **Excluir** ao lado da senha do aplicativo que você deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** para confirmar que deseja excluir a senha e, em seguida, selecione **Fechar**.

    A senha do aplicativo é excluída com sucesso.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e excluir senhas de aplicativos usando o portal do Office 365

Se você usa a verificação em duas etapas com sua conta do trabalho ou da escola e seus aplicativos do Office 365, pode criar e excluir suas senhas de aplicativos usando o portal do Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar senhas de aplicativos usando o portal do Office 365

1. Entre no Office 365 e, em seguida, acesse a [página Minha conta](https://portal.office.com), selecione **Segurança e privacidade** e, em seguida, expanda **Verificação de segurança adicional**.

    ![Do portal mostrando Office expandido a área de verificação de segurança adicional](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecione o texto que diz: **Crie e gerencie senhas de aplicativos** para abrir a página **Senhas de aplicativos**.

    ![Página Senhas do aplicativo, com a guia Senhas do aplicativo realçada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selecione **Criar**, digite o nome do aplicativo que requer a senha do aplicativo e selecione **Avançar**.

    ![Página Criar senhas do aplicativo, com o nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copie a senha da página **Sua senha do aplicativo** e selecione **Fechar**.

    ![Sua página de senha do aplicativo com a senha para seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na página **Senhas do aplicativo**, verifique se seu aplicativo está listado.

     ![Página de senhas do aplicativo, com o novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Abra o aplicativo para o qual você criou a senha do aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve fazer isso uma vez por aplicativo.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Para excluir senhas de aplicativo usando a página Senhas de aplicativo

1. Na página **Senhas do aplicativo** e, em seguida, selecione **Excluir** ao lado da senha do aplicativo que você deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** na caixa de confirmação e selecione **Fechar**.

    A senha do aplicativo é excluída com sucesso.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as senhas do aplicativo não estiverem funcionando corretamente

Certifique-se de ter digitado sua senha corretamente. Se tiver certeza de que você digitou sua senha corretamente, tente entrar novamente e criar uma nova senha do aplicativo. Se nenhuma dessas opções resolver seu problema, entre em contato com o Suporte técnico de sua organização para que eles possam excluir suas senhas de aplicativo existentes, permitindo que você crie outras.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicativo Autenticador Microsoft](user-help-auth-app-download-install.md) para verificar suas conexões com notificações de aplicativo, em vez de receber mensagens ou ligações.
