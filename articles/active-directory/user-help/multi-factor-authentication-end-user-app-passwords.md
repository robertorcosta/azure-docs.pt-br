---
title: Como gerenciar senhas de aplicativos - Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as senhas do aplicativo e para que elas são usadas em relação à verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253215"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerenciar senhas de aplicativos para verificação em duas etapas

>[!Important]
>O administrador pode não permitir que você use senhas de aplicativo. Caso você não veja a opção **Senhas de aplicativos**, elas não estão disponíveis em sua organização.

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas do aplicativo são geradas automaticamente e devem ser criadas e inseridas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, depois que a verificação em duas etapas estiver ativada, você não precisará mais de senhas de aplicativos para clientes do Office 2013. Para obter mais informações, consulte o artigo [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicativos

Durante seu processo inicial de registro de verificação de dois fatores, você é fornecido com uma única senha de aplicativo. Se você precisar de mais de um, você terá que criá-los você mesmo. Você pode criar senhas de aplicativos de várias áreas, dependendo de como a verificação de dois fatores é configurada em sua organização. Para obter mais informações sobre como se registrar para usar a verificação de dois fatores com sua conta de trabalho ou escola, consulte [Visão geral para verificação de dois fatores e sua conta de trabalho ou escola](multi-factor-authentication-end-user-first-time.md) e seus artigos relacionados.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e excluir suas senhas de aplicativo

Você pode criar e excluir senhas de aplicativos, com base em como você usa a verificação de dois fatores:

- **Sua organização usa verificação de dois fatores e a página de verificação de segurança adicional.** Se você estiver usando sua conta de alain@contoso.comtrabalho ou escola (como, ) com verificação de dois fatores em sua organização, você pode gerenciar suas senhas de aplicativo a partir da [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx). Para obter instruções [detalhadas, consulte Criar e excluir senhas de aplicativos usando a página de verificação de segurança adicional](#create-and-delete-app-passwords-from-the-additional-security-verification-page) neste artigo.

- **Sua organização usa verificação de dois fatores e o portal Office 365.** Se você estiver usando sua conta de alain@contoso.comtrabalho ou escola (como, ), verificação de dois fatores e aplicativos do Office 365 em sua organização, você pode gerenciar suas senhas de aplicativo a partir da página do [portal Office 365](https://www.office.com). Para obter instruções [detalhadas, consulte Criar e excluir senhas do aplicativo usando o portal Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) neste artigo.

- **Você está usando a verificação de dois fatores com uma conta microsoft pessoal.** Se você estiver usando uma conta pessoal alain@outlook.comda Microsoft (como, ) com verificação de dois fatores, você pode gerenciar suas senhas de aplicativo a partir da [página de base de Segurança](https://account.microsoft.com/security/). Para obter instruções [detalhadas, consulte Usando senhas de aplicativos com aplicativos que não suportam verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Crie e exclua senhas de aplicativos da página de verificação de segurança adicional

Você pode criar e excluir senhas de aplicativos da página **de verificação de segurança adicional** para sua conta de trabalho ou escola.

1. Faça login na [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx)e selecione **senhas do aplicativo**.

    ![Página de senhas do aplicativo, com a guia senhas do app destacada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selecione **Criar,** digite o nome do aplicativo que requer a senha do aplicativo e, em seguida, selecione **Next**.

    ![Crie a página senhas do aplicativo, com nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copie a senha da página de senha do **aplicativo** e selecione **Fechar**.

    ![Sua página de senha do aplicativo com a senha para o seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na página senhas do **App,** certifique-se de que seu aplicativo está listado.

     ![Página de senhas do aplicativo, com novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Abra o aplicativo para o qual você criou a senha do aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve ter que fazer isso uma vez por aplicativo.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Para excluir uma senha do aplicativo usando a página senhas do App

1. Na página **senhas** do app, **selecione Excluir** ao lado da senha do aplicativo que deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** para confirmar que deseja excluir a senha e, em seguida, selecione **Fechar**.

    A senha do aplicativo é excluída com sucesso.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e excluir senhas de aplicativos usando o portal do Office 365

Se você usa a verificação em duas etapas com sua conta do trabalho ou da escola e seus aplicativos do Office 365, pode criar e excluir suas senhas de aplicativos usando o portal do Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar senhas de aplicativos usando o portal do Office 365

1. Faça login no Office 365 e vá até a [página Minha conta,](https://portal.office.com)selecione **Segurança & privacidade**e, em seguida, **expanda verificação de segurança adicional**.

    ![Do portal mostrando Office expandido a área de verificação de segurança adicional](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecione o texto que diz, **Crie e gerencie senhas de aplicativos** para abrir a página senhas do **App.**

    ![Página de senhas do aplicativo, com a guia senhas do app destacada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selecione **Criar,** digite o nome do aplicativo que requer a senha do aplicativo e, em seguida, selecione **Next**.

    ![Crie a página senhas do aplicativo, com nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copie a senha da página de senha do **aplicativo** e selecione **Fechar**.

    ![Sua página de senha do aplicativo com a senha para o seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na página senhas do **App,** certifique-se de que seu aplicativo está listado.

     ![Página de senhas do aplicativo, com novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Abra o aplicativo para o qual você criou a senha do aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve ter que fazer isso uma vez por aplicativo.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Para excluir senhas do aplicativo usando a página senhas do App

1. Na página **senhas** do app, **selecione Excluir** ao lado da senha do aplicativo que deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** na caixa de confirmação e, em seguida, selecione **Fechar**.

    A senha do aplicativo é excluída com sucesso.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as senhas do aplicativo não estiverem funcionando corretamente

Certifique-se de ter digitado sua senha corretamente. Se tiver certeza de que você digitou sua senha corretamente, tente entrar novamente e criar uma nova senha do aplicativo. Se nenhuma dessas opções corrigir seu problema, entre em contato com o help desk da sua organização para que eles possam excluir suas senhas de aplicativos existentes, permitindo que você crie novas.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicativo Autenticador Microsoft](user-help-auth-app-download-install.md) para verificar suas conexões com notificações de aplicativo, em vez de receber mensagens ou ligações.
