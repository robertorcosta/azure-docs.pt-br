---
title: Início Rápido da redefinição de senha de autoatendimento do Azure AD
description: Neste início rápido, rapidamente você irá configurar a redefinição de senha de autoatendimento do Azure AD para permitir que os usuários redefinam suas próprias senhas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582a6a32aa4c34b2e6fef37f3de5b5414de16cf3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846650"
---
# <a name="quickstart-self-service-password-reset"></a>Início Rápido: Redefinição de senha de autoatendimento

Neste início rápido, você passa pela configuração da redefinição de senha de autoatendimento (SSPR) como um meio simples para os administradores de TI habilitarem os usuários para redefinir suas senhas ou desbloquear suas contas.

## <a name="prerequisites"></a>Pré-requisitos

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada.
* Uma conta com privilégios de Administrador Global.
* Um usuário de teste não administrador com uma senha que você sabe. Se você precisar criar um usuário, confira o artigo [Início Rápido: Adicionar novos usuários ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar que o usuário de teste não-administrador é membro, ou se você precisar criar um grupo, confira o artigo [Criar um grupo e adicionar membros ao Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

[Exibir este processo como um vídeo no YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. Do seu locatário existente do Azure AD, do menu do portal do Azure ou da **Página Inicial**, selecione o **Azure Active Directory**. Em seguida, selecione **Redefinição de senha**.

2. Na página **Propriedades**, na opção **Redefinição da Senha de Autoatendimento Habilitada**, escolha **Selecionado**.
    * Em **Selecionar grupo**, escolha o grupo piloto criado como parte da seção de pré-requisitos deste artigo.
    * Clique em **Save** (Salvar).

3. Na página **Métodos de autenticação**, escolha o seguinte:
   * Número de métodos necessários para redefinir: **1**
   * Métodos disponíveis para os usuários:
      * **Email**
      * **Código de aplicativo móvel (versão prévia)**
   * Clique em **Save** (Salvar).

     ![Escolhendo métodos de autenticação para SSPR][Authentication]

4. Na página **Registro**, escolha o seguinte:
   * Exigir que os usuários se cadastram ao entrarem: **Sim**
   * Definir o número de dias antes que os usuários precisem reconfirmar suas informações de autenticação: **365**

## <a name="test-self-service-password-reset"></a>Testar redefinição de senha de autoatendimento

Agora, vamos testar sua configuração de SSPR com um usuário de teste. Uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure, testar usando uma conta de administrador pode alterar o resultado. Para obter mais informações sobre a política de senha do administrador, confira o nosso artigo de [política de senha](concept-sspr-policy.md).

1. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Entre com um usuário de teste não-administrador e registre seu telefone de autenticação.
3. Uma vez concluído, clique no botão marcado **parece bom** e feche a janela do navegador.
4. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://aka.ms/sspr](https://aka.ms/sspr).
5. Insira a ID de usuário dos seus usuários de teste não-administrador, os caracteres de CAPTCHA e, em seguida, clique em **Avançar**.
6. Siga as etapas de verificação para redefinir sua senha

## <a name="clean-up-resources"></a>Limpar recursos

É fácil desabilitar a redefinição de senha por autoatendimento. Abra o seu locatário do Azure AD e vá para **Propriedades** > **Redefinição de Senha** e, em seguida, selecione **Nenhum** em **Redefinição de Senha do Serviço de Autoatendimento Habilitada**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a configurar rapidamente a redefinição de senha de autoatendimento para seus usuários somente nuvem. Para saber como concluir uma distribuição mais detalhada, continue para nosso guia de distribuição.

> [!div class="nextstepaction"]
> [Distribuir a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"
