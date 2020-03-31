---
title: Registre informações de autenticação para redefinir sua própria senha - Azure AD
description: Registre as informações do seu método de verificação para redefinição de senha de autoatendimento do Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062635"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre as informações do seu método de verificação para redefinir sua própria senha

Caso você tenha esquecido sua senha corporativa ou de estudante, nunca tenha recebido uma da empresa ou sua conta tenha sido bloqueada, você pode usar suas informações de segurança e o dispositivo móvel para redefinir sua senha corporativa ou de estudante.

O administrador deve ativar esse recurso para que você possa registrar suas informações e redefinir sua própria senha. Se você não vir a opção **Esqueci minha senha,** significa que o administrador não ligou o recurso para sua organização. Se você acredita que isso esteja incorreto, entre em contato com o suporte técnico para obter ajuda.

>[!Important]
>Este artigo destina-se a usuários que tentam usar o cadarção para redefinição de senha de autoatendimento. Isso significa que você poderá redefinir sua própria senha de alain@contoso.comtrabalho ou escola (como, sem precisar da ajuda do administrador. Se você é um administrador procurando informações sobre como ativar a redefinição de senha de autoatendimento para seus funcionários ou outros usuários, consulte a redefinição da [senha de autoatendimento do Deploy Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configure seu método de verificação de redefinição de senha

1. Abra o navegador da Web em seu dispositivo e vá para a [página de informações de segurança](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Dependendo de como o administrador configurou sua organização, uma ou mais das seguintes opções estarão disponíveis para você configurar como seu método de verificação de segurança. Se várias opções estiverem disponíveis, recomendamos fortemente que você use mais de uma como seu método de verificação de segurança, caso um de seus métodos fique indisponível.

    - **Aplicativo de autenticação.** Escolha usar o aplicativo Microsoft Authenticator ou outro aplicativo autenticador como seu método de verificação de segurança. Para obter mais informações sobre como configurar o aplicativo, consulte [Configurar o aplicativo Microsoft Authenticator como seu método de verificação](security-info-setup-auth-app.md).

    - **Mensagens de texto.** Escolha enviar mensagens de texto para o seu dispositivo móvel. Para obter mais informações sobre como configurar mensagens de texto, consulte [Configurar mensagens de texto como seu método de verificação](security-info-setup-text-msg.md).

    - **Telefonemas.** Escolha receber uma ligação para o seu número de telefone registrado. Para obter mais informações sobre como configurar chamadas telefônicas, consulte [Configurar um número de telefone como seu método de verificação](security-info-setup-phone-number.md).

    - **Chave de segurança.** Escolha usar uma chave de segurança compatível com a Microsoft. Para obter mais informações, consulte [Configurar uma chave de segurança como seu método de verificação](security-info-setup-security-key.md).

    - **Endereço de e-mail.** Escolha usar um endereço de e-mail alternativo que possa ser usado sem exigir sua senha esquecida ou ausente. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre como configurar um endereço de e-mail, consulte [Configurar um endereço de e-mail como seu método de verificação](security-info-setup-email.md).

    - **Perguntas de segurança.** Escolha configurar e responder a perguntas de segurança pré-definidas configuradas pelo administrador. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre questões de segurança, consulte [Configurar perguntas de segurança como seu método de verificação](security-info-setup-questions.md).

3. Depois de selecionar e configurar seus métodos, escolha **Concluir** para concluir o processo.

    > [!Note]
    > As informações adicionadas para seu número de telefone ou endereço de e-mail não são compartilhadas com o diretório global da sua organização. As únicas pessoas que podem ver essas informações são você e seu administrador. Só você pode ver as respostas para suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Mensagem de erro |  Solução possível |
| --- | --- | --- |
| Contate o administrador.<br>Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente.<br>Entre em contato com sua equipe de TI para obter assistência.| Se você receber esta mensagem de erro depois de digitar seu ID de usuário, significa que sua organização gerencia internamente sua senha e não quer que você redefini-lo a partir do link **Não pode acessar sua conta.** Para redefinir sua senha nesta situação, você deve entrar em contato com o balcão de ajuda da sua organização ou com o administrador para obter ajuda. |
| Sua conta não está habilitada para redefinição de senha.<br>A equipe de TI não configurou sua conta para usar esse serviço.<br>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Se você receber essa mensagem de erro depois de digitar seu ID de usuário, significa que ou sua organização não ligou o recurso de redefinição de senha ou você não está autorizado a usá-lo. Para redefinir sua senha nesta situação, você deve selecionar o **link Entrar em contato com um administrador.** Depois de clicar no link, um e-mail é enviado para o help desk ou administrador da sua organização, informando que você deseja redefinir sua senha. |
| Não foi possível verificar sua conta.<br>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Se você receber esta mensagem de erro depois de digitar seu ID de usuário, significa que sua organização ligou a redefinição de senha e que você pode usá-la, mas que você não se registrou para o serviço. Nesta situação, você deve entrar em contato com o help desk ou administrador da sua organização para redefinir sua senha. Para obter informações sobre como se registrar para redefinição de senha depois que você voltar ao seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Próximas etapas

- [Alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de redefinição de senha](https://passwordreset.microsoftonline.com/)

- [Quando você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
