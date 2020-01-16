---
title: Personalizar autoatendimento de redefinição de senha-Azure Active Directory
description: Opções de personalização para redefinição de senha por autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979451"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD

Os profissionais de TI que desejam implantar a redefinição de senha de autoatendimento (SSPR) no Azure Active Directory (AD do Azure) podem personalizar a experiência para atender às necessidades de seus usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Os usuários de redefinição de senha de autoatendimento têm um link "Contate o administrador" disponível para eles no portal de redefinição de senha. Se um usuário selecionar esse link, ele fará uma destas duas coisas:

* Se deixado no estado padrão:
   * O email é enviado aos seus administradores e solicita que eles forneçam assistência para alterar a senha do usuário. Consulte o [email de exemplo](#sample-email) abaixo.
* Se personalizado:
   * Envia o usuário para uma página da Web ou endereço de email especificado pelo administrador para obter assistência.

> [!TIP]
> Se você personalizar isso, é recomendável definir isso para algo que os usuários já conhecem para obter suporte

> [!WARNING]
> Se você personalizar essa configuração com um endereço de email e uma conta que precise de uma redefinição de senha, talvez o usuário não possa solicitar assistência.

### <a name="sample-email"></a>Email de exemplo

![Exemplo de solicitação para redefinir email enviado ao administrador][Contact]

Esse contato é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **administrador de assistência técnica** ou a função de **administrador de senha** for atribuída, os administradores com essas funções serão notificados.
1. Se não forem atribuídos administradores de assistência técnica ou administrador de senha, os administradores com a função de **administrador de usuários** serão notificados.
1. Se nenhuma das funções anteriores for atribuída, os **administradores globais** serão notificados.

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferentes funções de administrador e sobre como atribuí-las, consulte [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails "Contate o administrador"

Caso sua organização não queira que os administradores recebam solicitações de redefinição de senha, você poderá habilitar a configuração a seguir:

* Habilite o autoatendimento de redefinição de senha para todos os usuários finais. Essa opção pode ser encontrada em **Redefinição de Senha** > **Propriedades**. Se você não quiser que os usuários redefinam as próprias senhas, poderá definir o escopo de acesso como um grupo vazio. *Não recomendamos essa opção.*
* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Essa opção pode ser encontrada em **Redefinição de Senha** > **Personalização** > **URL ou email de assistência técnica personalizados**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalize a página de entrada do AD FS para SSPR

Os administradores do Active Directory Federation Services (AD FS) podem adicionar um link à página de entrada deles usando diretrizes no artigo [Adicionar descrição da página de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Para adicionar um link à página de entrada do AD FS, use o seguinte comando no servidor do AD FS. Os usuários podem usar essa página para inserir o fluxo de trabalho de SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar a página de entrada e a aparência do painel de acesso

Você pode personalizar a página de entrada. Você pode adicionar um logotipo que aparece junto com a imagem adequada à identidade visual de sua empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
   * Passando o parâmetro `whr` para a página de redefinição de senha, como `https://login.microsoftonline.com/?whr=contoso.com`
   * Passando o parâmetro `username` para a página de redefinição de senha, como `https://login.microsoftonline.com/?username=admin@contoso.com`

Encontre detalhes sobre como configurar a identidade visual da empresa no artigo [Adicionar uma identidade visual da empresa à página de entrada do Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nome do diretório

Você pode alterar o atributo de nome de diretório em **Azure Active Directory** > **Propriedades**. Você pode mostrar um nome amigável de organização que aparece no portal e nas comunicações automatizadas. Essa opção fica mais visível nos emails automatizados nos formatos a seguir:

* O nome amigável no email, por exemplo, "Microsoft em nome da demonstração da CONTOSO"
* A linha do assunto no email, por exemplo, "Código de verificação de email da conta de demonstração da CONTOSO"

## <a name="next-steps"></a>Próximos passos

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contate o administrador para obter ajuda com a redefinição do exemplo de email de senha"
