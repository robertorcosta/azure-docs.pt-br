---
title: Adicione uma conta microsoft pessoal ao aplicativo Microsoft Authenticator - Azure AD
description: Adicione contas pessoais da Microsoft, como para Outlook.com ou Xbox LIVE ao aplicativo Microsoft Authenticator para verificar sua identidade enquanto usa a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984721"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicione contas pessoais da Microsoft ao aplicativo Microsoft Authenticator

Adicione suas contas pessoais da Microsoft, como para contas Outlook.com e Xbox LIVE, ao aplicativo Microsoft Authenticator para o processo padrão de verificação de dois fatores e login de telefone sem senha.

- **Método de verificação padrão de dois fatores.** Digite seu nome de usuário e senha no dispositivo em que você está fazendo login e escolha se o aplicativo Microsoft Authenticator envia uma notificação ou se você prefere copiar o código de verificação associado da tela **contas** do aplicativo Microsoft Authenticator.

- **Método de entrada sem senha.** Digite seu nome de usuário no dispositivo que você está fazendo login para usar sua conta microsoft pessoal e, em seguida, use seu dispositivo móvel para verificar se é você usando sua impressão digital, rosto ou PIN. Para esse método, você não precisa inserir a senha.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

Você pode adicionar sua conta Microsoft pessoal ativando primeiro a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo. Você não precisa ativar a verificação de dois fatores para usar apenas o login de telefone sem senha para sua conta, mas recomendamos fortemente que você ative a verificação de dois fatores para segurança adicional da conta.

## <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá para a página [Noções básicas sobre segurança](https://account.microsoft.com/security) e entre usando sua conta Microsoft pessoal. Por exemplo, alain@outlook.com.

2. Na parte inferior da página **Noções básicas de segurança**, escolha o link **mais opções de segurança**.

    ![Noções básicas de segurança com o link "mais opções de segurança" realçado](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá para a seção **verificação em duas etapas** e opte por **Ativar** o recurso. Você também poderá desativá-lo aqui se não quiser mais usá-lo com sua conta pessoal.

## <a name="add-your-microsoft-account-to-the-app"></a>Adicionar a conta Microsoft ao aplicativo

1. Abra o aplicativo Microsoft Authenticator no dispositivo móvel.

1. No Android, **selecione Adicionar conta** no ícone Personalizar e **Controlar** no canto superior direito.

    ![Páginas de seleção de contas Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    No iOS, selecione o ícone de adição no canto superior direito.

    ![versão do iOS da experiência de seleção de conta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na página **Adicionar conta**, escolha **Conta pessoal**.

1. Selecione **Entrar com** a Microsoft para adicionar sua conta. Um código QR pode ser usado quando disponível, mas você sempre pode adicionar sua conta fazendo login com seu nome de usuário e senha.

    ![Selecione uma conta Microsoft ou escaneie um código QR quando estiver disponível](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Faça login na sua conta pessoal, usando alain@outlook.como endereço de e-mail apropriado (como), e selecione **Next**.

    >[!Note]
    >Se você não tem uma conta pessoal da Microsoft, [você pode criar uma](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Digite sua senha e selecione **Entrar .** Sua conta pessoal é adicionada ao aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para sua conta microsoft pessoal, consulte a seção Problemas de código de verificação de **problemas** do artigo [de informações de segurança da conta da Microsoft & códigos de verificação.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
