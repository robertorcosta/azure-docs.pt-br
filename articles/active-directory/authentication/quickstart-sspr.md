---
title: Início Rápido – Redefinição de senha por autoatendimento do Azure AD
description: Neste guia de início rápido, você aprende a configurar a redefinição de senha por autoatendimento do Azure AD para permitir que os usuários redefinam as próprias senhas, reduzindo a sobrecarga para o departamento de TI.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154849"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Início Rápido: Configurar redefinição de senha por autoatendimento do Azure Active Directory

Neste guia de início rápido, você configura a SSPR (redefinição de senha por autoatendimento) do Azure Active Directory (AD) para permitir que os usuários redefinam as próprias senhas ou desbloqueiem as próprias contas. Com a SSPR, os usuários podem redefinir as próprias credenciais sem assistência técnica ou administrador. Essa capacidade permite que os usuários recuperem o acesso à própria conta sem esperar suporte adicional.

> [!IMPORTANT]
> Este guia de início rápido mostra a um administrador como habilitar a redefinição de senha por autoatendimento. Se você for um usuário final já registrado para redefinição de senha por autoatendimento e precisar voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

## <a name="prerequisites"></a>Prerequisites

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de Administrador Global.
* Um usuário de teste que não seja administrador com uma senha que você conheça, como *testuser*.
    * Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar do qual o usuário de teste não administrador seja membro, como *SSPR-Test-Group*.
    * Se você precisar criar um grupo, confira como [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

[Exibir este processo como um vídeo no YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. No menu do portal do Azure ou na página **Inicial**, selecione **Azure Active Directory** e, em seguida, escolha **Redefinição de senha**.

1. Na página **Propriedades**, na opção **Redefinição da Senha por Autoatendimento Habilitada**, escolha **Selecionado**.
1. Escolha **Selecionar grupo** e, em seguida, selecione o grupo piloto criado como parte da seção pré-requisitos deste artigo, como *SSPR-Test-Group*. Quando estiver pronto, selecione **Salvar**.
1. Na página **Métodos de autenticação**, escolha o seguinte e depois escolha **Salvar**:
    * Número de métodos necessários para redefinir: **1**
    * Métodos disponíveis para os usuários:
        * **Código do aplicativo móvel**
        * **Email**

    > [!div class="mx-imgBorder"]
    > ![Escolhendo métodos de autenticação para SSPR][Authentication]

4. Na página **Registro**, escolha o seguinte e então escolha **Salvar**:
   * Exigir que os usuários se cadastram ao entrarem: **Sim**
   * Definir o número de dias antes que os usuários precisem reconfirmar suas informações de autenticação: **365**

## <a name="test-self-service-password-reset"></a>Testar redefinição de senha de autoatendimento

Agora, vamos testar a configuração do SSPR com um usuário de teste que faz parte do grupo selecionado na seção anterior, como *testuser*. Uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure, testar usando uma conta de administrador pode alterar o resultado. Para obter mais informações sobre a política de senha do administrador, confira o nosso artigo de [política de senha](concept-sspr-policy.md).

1. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Entre com um usuário de teste não-administrador, como *testuser*, e registre seu telefone de autenticação.
3. Uma vez concluído, selecione o botão marcado **Parece bom** e feche a janela do navegador.
4. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://aka.ms/sspr](https://aka.ms/sspr).
5. Insira a ID de usuário dos seus usuários de teste não administradores, como **testuser**, os caracteres do CAPTCHA e, em seguida, selecione *Avançar*.
6. Siga as etapas de verificação para redefinir sua senha.

## <a name="clean-up-resources"></a>Limpar os recursos

Para desabilitar a redefinição de senha por autoatendimento, pesquise e selecione **Azure Active Directory** no portal do Azure. Selecione **Redefinição de Senha** e, em seguida, escolha **Nenhum** em **Redefinição de Senha Self-service Habilitada**. Quando estiver pronto, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a configurar a redefinição de senha por autoatendimento para seus usuários somente nuvem. Para saber como concluir uma distribuição mais detalhada, continue para nosso guia de distribuição.

> [!div class="nextstepaction"]
> [Distribuir a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
