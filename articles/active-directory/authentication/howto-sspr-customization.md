---
title: Personalizar redefinição de senha de autoatendimento - Azure Active Directory
description: Saiba como personalizar as opções de exibição e experiência do usuário para redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394248"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalize a experiência do usuário para redefinição de senha de autoatendimento do Azure Active Directory

O reset de senha de autoatendimento (SSPR) dá aos usuários do Azure Active Directory (Azure AD) a capacidade de alterar ou redefinir sua senha, sem envolvimento do administrador ou do help desk. Se a conta de um usuário estiver bloqueada ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

Para melhorar a experiência de SSPR para os usuários, você pode personalizar a aparência da página de redefinição de senha, notificações de e-mail ou páginas de login. Essas opções de personalização permitem que você deixe claro para o usuário que eles estão no lugar certo, e dar-lhes confiança de que estão acessando os recursos da empresa.
    
Este artigo mostra como personalizar o link de e-mail SSPR para usuários, marca da empresa e link de página de login do AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Para ajudar os usuários a procurar assistência com redefinição de senha de autoatendimento, um link "Entre no administrador" é mostrado no portal de redefinição de senhas. Se um usuário seleciona este link, ele faz uma de duas coisas:

* Se este link de contato for deixado no estado padrão, um e-mail será enviado aos seus administradores e pedirá que eles forneçam assistência para alterar a senha do usuário. O e-mail de exemplo a seguir mostra esta mensagem de e-mail padrão:

    ![Solicitação de amostra para redefinir e-mail enviado ao administrador](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizado, envia o usuário para uma página web ou endereço de e-mail especificado pelo administrador para assistência.
    * Se você personalizar isso, recomendamos definir isso para algo que os usuários já estão familiarizados com o suporte.

    > [!WARNING]
    > Se você personalizar essa configuração com um endereço de e-mail e uma conta que precise de uma redefinição de senha, o usuário pode não poder pedir ajuda.

### <a name="default-email-behavior"></a>Comportamento padrão de e-mail

O e-mail de contato padrão é enviado aos destinatários na seguinte ordem:

1. Se a função *de administrador do helpdesk* ou a função *de administrador de senhafor,* os administradores com essas funções serão notificados.
1. Se nenhum administrador de helpdesk ou administrador de senhafor, os administradores com a função *de administrador do usuário* serão notificados.
1. Se nenhuma das funções anteriores for atribuída, os *administradores globais serão notificados.*

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferentes funções de administrador e sobre como atribuí-las, consulte [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails "Contate o administrador"

Se sua organização não quiser notificar os administradores sobre solicitações de redefinição de senha, as seguintes opções de configuração podem ser usadas:

* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Esta opção está em **Password Reset** > **Customization Personalization** > **E-mail ou URL personalizado**.
* Habilite a redefinição de senha de autoatendimento para todos os usuários. Esta opção está em **Propriedades de redefinição de** > **senha**. Se você não quiser que os usuários redefinam as próprias senhas, poderá definir o escopo de acesso como um grupo vazio. *Não recomendamos essa opção.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalize a página de login e o painel de acesso

Você pode personalizar a página de login, como adicionar um logotipo que aparece junto com a imagem que se encaixa na marca da sua empresa. Para obter mais informações sobre como configurar a marca da empresa, consulte [Adicionar a marca da empresa à sua página de login no Azure AD](../fundamentals/customize-branding.md).

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
   * Passando o `whr` parâmetro para a página de redefinição de senha, como`https://login.microsoftonline.com/?whr=contoso.com`
   * Passando o `username` parâmetro para a página de redefinição de senha, como`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome do diretório

Para fazer as coisas parecerem mais fáceis de usar, você pode mudar o nome da organização no portal e nas comunicações automatizadas. Para alterar o atributo nome do diretório no portal Azure, navegue até **Azure Active Directory** > **Properties**. Esta opção amigável de nome de organização é a mais visível em e-mails automatizados, como nos exemplos a seguir:

* O nome amigável no e-mail, por exemplo "*Microsoft em nome da demo contoso*"
* A linha de assunto no e-mail, por exemplo , "*CONTOSO demo account verification code*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalize a página de login do AD FS

Se você usar o Active Directory Federation Services (AD FS) para eventos de login do usuário, você pode adicionar um link para a página de login usando a orientação no artigo para [adicionar a descrição da página de login](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Forneça aos usuários um link para a página para que eles *https://passwordreset.microsoftonline.com*insiram o fluxo de trabalho SSPR, como . Para adicionar um link à página de login do AD FS, use o seguinte comando no servidor AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Próximas etapas

Para entender o uso do SSPR em seu ambiente, consulte [Opções de relatórios para gerenciamento de senhas AD do Azure](howto-sspr-reporting.md).

Se você ou usuários tiverem problemas com o SSPR, consulte [Redefinir a senha de autoatendimento de Solução de Problemas](active-directory-passwords-troubleshoot.md)
