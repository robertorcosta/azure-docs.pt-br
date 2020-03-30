---
title: Login usando suas informações de autenticação de identidade - Azure AD
description: Saiba como fazer login usando os vários métodos de verificação de identidade em informações de segurança.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062159"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Faça login usando a verificação em duas etapas ou informações de segurança

Depois de configurar a verificação em duas etapas ou informações de segurança, você poderá entrar na sua conta usando o método de verificação especificado.

> [!Note]
> Se você ainda estiver usando a experiência de verificação em duas etapas, você precisará configurar seus métodos de verificação seguindo as instruções no Configurar minha conta para um artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)
>
> Se o administrador tiver ligado a experiência de informações de segurança, você precisará definir seus métodos de verificação usando esses artigos passo a passo:<ul><li>[Configurar informações de segurança para usar um aplicativo de autenticação](security-info-setup-auth-app.md)</li><li>[Configurar informações de segurança para usar mensagens de texto](security-info-setup-text-msg.md)</li><li>[Configure informações de segurança para usar uma chamada telefônica](security-info-setup-phone-number.md)</li><li>[Configure informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Faça login usando uma notificação de aplicativo do autenticador no seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e a senha.

2. Selecione **Aprovar** na notificação de aprovação enviada para seu dispositivo móvel.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Faça login usando um código de aplicativo do autenticador no seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e a senha.

2. Abra seu aplicativo autenticador e digite o código gerado aleatoriamente para sua conta na caixa **Inserir código**.

## <a name="sign-in-using-your-phone-number"></a>Conecte-se usando seu número de telefone

1. Entre em sua conta com seu nome de usuário e a senha.

2. Atenda seu telefone e siga as instruções.

## <a name="sign-in-using-a-text-message"></a>Entre usando uma mensagem de texto

1. Entre em sua conta com seu nome de usuário e a senha.

2. Abra a mensagem de texto e digite o código da sua mensagem de texto na caixa **Digitar código**.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Faça login usando uma chave de segurança na tela de bloqueio

1. Depois de registrar sua chave de segurança, selecione a imagem da chave de segurança da tela de bloqueio do Windows 10.

2. Insira sua chave de segurança na porta USB do dispositivo e faça login no Windows usando o PIN da chave de segurança.

    ![Entrada da chave de segurança na tela de bloqueio do Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Faça login usando uma chave de segurança e o navegador Microsoft Edge

1. Depois de registrar sua chave de segurança, abra o navegador Microsoft Edge.

2. Quando solicitado a fazer login, insira a chave de segurança na porta USB do dispositivo e faça login no Windows usando o PIN da chave de segurança.

    ![Login de chave de segurança usando o navegador Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Para obter informações sobre como fazer login usando o aplicativo Microsoft Authenticator, consulte o artigo, [faça login em suas contas usando o aplicativo Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Conecte-se usando outro método de verificação

Se por algum motivo você não puder usar seu método de login principal, você pode usar outro método de verificação previamente configurado.

1. Faça login em sua conta normalmente e escolha o **Link de outra forma** na página de **verificação em duas etapas.**

    ![Alterar o método de verificação de entrada](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se você não ver o **Link de outra forma,** significa que você não configurou nenhum outro método de verificação e que você terá que entrar em contato com o administrador para obter ajuda para entrar em sua conta. Depois que o administrador ajudar você a fazer login, certifique-se de adicionar métodos adicionais de verificação. Para obter mais informações sobre como adicionar métodos de verificação, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).
    >
    >Se você vir o **Link de outra forma,** mas ainda não ver outros métodos de verificação, você terá que entrar em contato com o administrador para obter ajuda para fazer login na sua conta.

2. Escolha seu método de verificação alternativo e continuar o processo de verificação em duas etapas.

3. Depois que você está em sua conta, você pode atualizar seus métodos de verificação (se necessário). Para obter mais informações sobre como adicionar ou mudar métodos, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as informações de segurança no artigo [Visão geral da informação de segurança (visualização)](user-help-security-info-overview.md).

- Saiba mais sobre a verificação em duas etapas no artigo [Visão geral da verificação em duas etapas](user-help-two-step-verification-overview.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/)

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
