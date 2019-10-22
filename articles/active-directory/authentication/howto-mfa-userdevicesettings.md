---
title: Administradores gerenciam usuários e dispositivos – Azure MFA-Azure Active Directory
description: Como os administradores podem alterar as configurações do usuário, como forçar os usuários a fazer o processo de verificação novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190d697dca56fa51d92987f32db0146aa79881aa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70162407"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a Autenticação Multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir a MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Gerenciar métodos de autenticação

Como um administrador atribuiu a função de administrador de autenticação, você pode exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revogar sessões de MFA existentes de seu objeto de usuário.

![Gerenciar métodos de autenticação do portal do Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory**  > **usuários**  > **todos os usuários**.
1. Escolha o usuário no qual você deseja executar uma ação e selecione **métodos de autenticação**.
   - **Redefinir senha** redefinirá a senha do usuário e atribuirá uma senha temporária que deve ser alterada na próxima entrada.
   - **Exigir o novo registro da MFA** fará com que, quando o usuário entrar na próxima vez, será solicitado a configurar um novo método de autenticação de MFA.
   - **Revogar sessões de MFA** limpa as sessões do MFA lembradas do usuário e exige que elas executem o MFA na próxima vez que ela for exigida pela política no dispositivo.

## <a name="require-users-to-provide-contact-methods-again"></a>Exigir que os usuários forneçam métodos de contato novamente

Essa configuração força o usuário a concluir o processo de registro novamente. Aplicativos sem navegador continuam funcionando se o usuário tiver senhas de aplicativos para eles.  Você pode excluir as senhas de aplicativos de usuários selecionando também **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os usuários selecionados forneçam métodos de contato novamente

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory**  > **usuários**  > **todos os usuários**.
3. À direita, selecione **autenticação multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa **Exigir que os usuários selecionados forneçam métodos de contato novamente**.
   ![Require que os usuários forneçam métodos de contato novamente ](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Clique em **Salvar**.
8. Clique em **Fechar**.

As organizações podem concluir essas etapas com o PowerShell usando o seguinte como um guia para limpar o atributo `StrongAuthenticationMethods`:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory**  > **usuários**  > **todos os usuários**.
3. À direita, selecione **autenticação multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![Delete todas as senhas de aplicativo existentes ](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Salvar**.
8. Clique em **Fechar**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar o MFA em todos os dispositivos lembrados de um usuário

Um dos recursos configuráveis da Autenticação Multifator do Azure está dando aos usuários a opção de marcar dispositivos como confiáveis. Para saber mais, confira [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Os usuários podem ignorar a verificação em duas etapas durante um período configurável em seus dispositivos regulares. Se uma conta for comprometida ou um dispositivo confiável for perdido, você precisa conseguir remover o status de confiável e exigir novamente a verificação em duas etapas.

Quando marcada, **restaure a autenticação multifator em todos os dispositivos lembrados** os usuários devem executar a verificação em duas etapas na próxima vez que entrarem, mesmo que tenham marcado seu dispositivo como confiável.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como Restaurar a MFA em todos os dispositivos suspensos para um usuário

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory**  > **usuários**  > **todos os usuários**.
3. À direita, selecione **autenticação multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa para **restaurar a autenticação multifator em todos os dispositivos lembrados** 
    ![Restore autenticação multifator em todos os dispositivos lembrados ](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Clique em **Salvar**.
8. Clique em **Fechar**.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre como [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, direcione-os para [Guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
