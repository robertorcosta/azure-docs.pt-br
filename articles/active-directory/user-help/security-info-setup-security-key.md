---
title: Configure uma chave de segurança como seu método de verificação - Azure AD
description: Como configurar sua página de segurança (visualização) para verificar sua identidade para usar uma chave de segurança FIDO2 (Fast Identity Online) como seu método de verificação.
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
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062329"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Configure uma chave de segurança como seu método de verificação

Você pode usar chaves de segurança como um método de login sem senha dentro de sua organização. Uma chave de segurança é um dispositivo físico que é usado com um PIN exclusivo para fazer login na sua conta de trabalho ou escola. Como as chaves de segurança exigem que você tenha o dispositivo físico e algo que só você sabe, ele é considerado um método de verificação mais forte do que um nome de usuário e senha.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se você não ver a opção de chave de segurança, é possível que sua organização não permita que você use essa opção para verificação. Neste caso, você precisará escolher outro método ou entrar em contato com o help desk da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Autenticação por redefinição de senha versus verificação de segurança

Métodos de informações de segurança são usados para a verificação de segurança de dois fatores e a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Usado para |
| ------ | -------- |
| Aplicativo autenticador | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Mensagens de texto | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chamadas telefônicas | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chave de segurança | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Conta de email | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |
| Perguntas de segurança | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |

## <a name="what-is-a-security-key"></a>O que é uma chave de segurança?

Atualmente, suportamos vários projetos e provedores de chaves de segurança usando os protocolos de autenticação sem senha [FIDO (FiDO2) fast identity online](https://fidoalliance.org/fido2/) (FIDO2). Essas chaves permitem que você faça login em sua conta de trabalho ou escola para acessar os recursos baseados em nuvem da sua organização quando em um dispositivo e navegador web suportados.

O administrador ou sua organização fornecerá uma chave de segurança se eles precisarem dela para o seu trabalho ou conta escolar. Existem diferentes tipos de chaves de segurança que você pode usar, por exemplo, uma chave USB que você conecta ao seu dispositivo ou uma chave NFC que você toca em um leitor NFC. Você pode obter mais informações sobre sua chave de segurança, incluindo que tipo é, a partir da documentação do fabricante.

> [!Note]
> Se você não puder usar uma chave de segurança FIDO2, existem outros métodos de verificação sem senha que você pode usar, como o aplicativo Microsoft Authenticator ou o Windows Hello. Para obter mais informações sobre o aplicativo Microsoft Authenticator, consulte [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md) Para obter mais informações sobre o Windows Hello, consulte [visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de começar

Antes de registrar sua chave de segurança, o seguinte deve ser verdadeiro:

- O administrador aumentou esse recurso para uso dentro da sua organização.

- Você está em um dispositivo executando o Windows 10 May 2019 Update e usando um navegador suportado.

- Você tem uma chave de segurança física aprovada pelo administrador ou pela sua organização. Sua chave de segurança deve ser compatível com FIDO2 e Microsoft. Se você tiver alguma dúvida sobre sua chave de segurança e se ela é compatível, entre em contato com o help desk da sua organização.

## <a name="register-your-security-key"></a>Registre sua chave de segurança

Você deve criar sua chave de segurança e dar-lhe um PIN exclusivo antes de poder entrar na sua conta de trabalho ou escola usando a chave. Você pode ter até 10 chaves registradas em sua conta. 

1. Acesse a página Meu https://myprofile.microsoft.com **perfil** e faça login se você ainda não fez isso.

2. Selecione **Informações de segurança,** **selecione Adicionar método**e, em seguida, selecione A **tecla Segurança** na lista de paradas do método Adicionar um **método.**

    ![Adicionar caixa de método, com a chave de segurança selecionada](media/security-info/security-info-security-key-add-method.png)

3. Selecione **Adicionar**e selecione o tipo de chave de segurança que você tem, dispositivo **USB** ou **dispositivo NFC**.

    ![Escolha se você tem uma chave de segurança usb ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se você não tiver certeza de que tipo de chave de segurança você tem, consulte a documentação do fabricante. Se você não tiver certeza sobre o fabricante, entre em contato com o help desk da sua organização para obter assistência.

4. Tenha sua chave de segurança fisicamente disponível e, em seguida, na caixa **de chaves de segurança,** selecione **Next**.

    ![Caixa de registro de início da chave de segurança](media/security-info/security-info-security-key-start-setup.png)

    Uma nova caixa parece ajudar a levá-lo através da configuração do seu novo método de login.

5. Na **configuração** da nova caixa de método de login, selecione **Next**e, em seguida:

    - Se a chave de segurança for um dispositivo USB, insira a chave de segurança na porta USB do seu dispositivo.

    - Se sua chave de segurança for um dispositivo NFC, toque na chave de segurança do leitor.

6. Digite seu PIN de chave de segurança exclusivo na caixa **de segurança** do Windows e, em seguida, selecione **OK**.

    Você retornará à configuração da sua nova caixa **de método de login.**

7. Selecione **Avançar**.

8. Retorne à página **de informações de segurança,** digite um nome que você reconhecerá mais tarde para sua nova chave de segurança e, em seguida, selecione **Next**.

    ![Página de informações de segurança, nomeando sua chave de segurança](media/security-info/security-info-security-key-name.png)

    Sua chave de segurança está registrada e pronta para você usar para fazer login na sua conta de trabalho ou escola.

9. Selecione **Feito** para fechar a caixa **de chaves de** segurança.

    A página **de informações de segurança** é atualizada com suas informações-chave de segurança.

    ![Página de informações de segurança, com todos os métodos registrados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Exclua uma chave de segurança de suas informações de segurança

Se você extraviar ou não quiser mais usar uma chave de segurança, você pode excluir a chave de suas informações de segurança. Embora isso impeça que a chave de segurança seja usada com sua conta de trabalho ou escola, a chave de segurança continua a armazenar seus dados e informações de credenciais. Para excluir seus dados e informações de credencial da própria chave de segurança, você deve seguir as instruções na [redefinir uma](#reset-your-security-key) seção de chave de segurança compatível com a Microsoft deste artigo.

1. Selecione o link **Excluir** da chave de segurança a ser removido.

2. Selecione **Ok** na caixa **de chaves de segurança Excluir.**

    Sua chave de segurança é excluída e você não poderá mais usá-la para entrar na sua conta de trabalho ou escola.

>[!Important]
>Se você excluir uma chave de segurança por engano, você pode registrá-la novamente usando as instruções na [seção Como registrar sua chave](#register-your-security-key) de segurança deste artigo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gerencie as configurações da chave de segurança a partir das configurações do Windows

Você pode gerenciar as configurações da chave de segurança a partir do aplicativo Configurações do **Windows,** incluindo a redefinição da chave de segurança e a criação de um pin de nova chave de segurança.

### <a name="reset-your-security-key"></a>Redefinir sua chave de segurança

Se você quiser excluir todas as informações da conta armazenadas em sua chave de segurança física, você deve devolver a chave de volta aos seus padrões de fábrica. A redefinição da chave de segurança exclui tudo da chave, permitindo que você recomece.

>[!IMPORTANT]
>A redefinição da chave de segurança exclui tudo da chave, redefinindo-a para padrões de fábrica.
>
> **Todos os dados e credenciais serão limpos.**

#### <a name="to-reset-your-security-key"></a>Para redefinir sua chave de segurança

1. Abra o aplicativo Configurações do Windows, selecione **Contas,** selecione **Opções de login,** selecione **Tecla de segurança**e selecione **Gerenciar**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.

3. Siga as instruções na tela, com base no fabricante específico da chave de segurança. Se o fabricante da chave não estiver listado nas instruções na tela, consulte o site do fabricante para obter mais informações.

4. Selecione **Fechar** para fechar a tela **Gerenciar.**

### <a name="create-a-new-security-key-pin"></a>Crie um novo PIN de chave de segurança

Você pode criar um pin de chave de segurança novo para sua chave de segurança.

#### <a name="to-create-a-new-security-key-pin"></a>Para criar um pin de chave de segurança novo

1. Abra o aplicativo Configurações do Windows, selecione **Contas,** selecione **Opções de login,** selecione **Tecla de segurança**e selecione **Gerenciar**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.
3. Selecione **Adicionar** na área **PIN da chave de segurança,** digite e confirme seu novo PIN da chave de segurança e, em seguida, selecione **OK**.

     A chave de segurança é atualizada com o novo PIN da chave de segurança para uso com sua conta de trabalho ou escola. Se você decidir alterar seu PIN novamente, você pode selecionar o botão **Alterar.**
4. Selecione **Fechar** para fechar a tela **Gerenciar.**

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Para registrar uma chave de segurança, você deve ter pelo menos um método adicional de verificação de segurança registrado. Consulte a [seção Visão Geral](security-info-add-update-methods-overview.md) para obter mais informações. 

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre métodos de verificação sem senha, leia que o [Azure AD da Microsoft inicia a visualização pública das chaves de segurança FIDO2, permitindo logins sem senha](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou leia o [aplicativo Qual é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md) [Windows Hello overview](https://www.microsoft.com/windows/windows-hello)

- Para obter informações mais detalhadas sobre [as chaves de segurança compatíveis com a Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
