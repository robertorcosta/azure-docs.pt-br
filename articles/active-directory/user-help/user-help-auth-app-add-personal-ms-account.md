---
title: Adicionar um conta Microsoft pessoal ao aplicativo Microsoft Authenticator-Azure AD
description: Adicione contas pessoais da Microsoft, como para Outlook.com ou Xbox LIVE ao aplicativo Microsoft Authenticator para verificar sua identidade ao usar a verificação de dois fatores.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984721"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicionar contas pessoais da Microsoft ao aplicativo Microsoft Authenticator

Adicione suas contas pessoais da Microsoft, como para contas do Outlook.com e do Xbox LIVE, ao aplicativo Microsoft Authenticator para o processo de verificação padrão de dois fatores e a entrada no telefone sem senha.

- **Método de verificação padrão de dois fatores.** Digite seu nome de usuário e senha no dispositivo no qual você está entrando e, em seguida, escolha se o aplicativo Microsoft Authenticator envia uma notificação ou se você prefere copiar o código de verificação associado da tela **contas** do aplicativo Microsoft Authenticator.

- **Método de entrada sem senha.** Digite seu nome de usuário no dispositivo no qual você está entrando usando seu conta Microsoft pessoal e, em seguida, use seu dispositivo móvel para verificar se é você está usando sua impressão digital, face ou PIN. Para esse método, você não precisa inserir a senha.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

Você pode adicionar sua conta Microsoft pessoal ativando primeiro a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo. Você não precisa ativar a verificação de dois fatores para usar somente a entrada por telefone sem senha para sua conta, mas é altamente recomendável ativar a verificação de dois fatores para segurança de conta adicional.

## <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá para a página [Noções básicas sobre segurança](https://account.microsoft.com/security) e entre usando sua conta Microsoft pessoal. Por exemplo, alain@outlook.com.

2. Na parte inferior da página **Noções básicas de segurança**, escolha o link **mais opções de segurança**.

    ![Noções básicas de segurança com o link "mais opções de segurança" realçado](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá para a seção **verificação em duas etapas** e opte por **Ativar** o recurso. Você também poderá desativá-lo aqui se não quiser mais usá-lo com sua conta pessoal.

## <a name="add-your-microsoft-account-to-the-app"></a>Adicionar a conta Microsoft ao aplicativo

1. Abra o aplicativo Microsoft Authenticator no dispositivo móvel.

1. No Android, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito.

    ![Páginas de seleção de conta do Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    No iOS, selecione o ícone de adição no canto superior direito.

    ![versão do iOS da experiência de seleção de conta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na página **Adicionar conta**, escolha **Conta pessoal**.

1. Selecione **entrar com a conta da Microsoft** para adicionar sua conta. Um código QR pode ser usado quando disponível, mas você sempre pode adicionar sua conta entrando com seu nome de usuário e senha.

    ![Selecione uma conta Microsoft ou Digitalize um código QR quando disponível](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Entre em sua conta pessoal, usando o endereço de email apropriado (como alain@outlook.com) e, em seguida, selecione **Avançar**.

    >[!Note]
    >Se você não tiver um conta Microsoft pessoal, [poderá criar um](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Insira sua senha e, em seguida, selecione **entrar**. Sua conta pessoal é adicionada ao aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção **Solucionando** problemas de código de verificação do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
