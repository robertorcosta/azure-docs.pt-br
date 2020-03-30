---
title: Nova experiência de registro de aplicativo do portal do Azure
titleSuffix: Microsoft identity platform
description: Uma introdução à nova experiência de registro de App no portal Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154586"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>A nova experiência de registro de aplicativos do portal Azure

Há muitas melhorias na experiência de registro do novo [App](https://go.microsoft.com/fwlink/?linkid=2083908) no portal Azure. Se você estiver mais familiarizado com a experiência do portal de inscrição (apps.dev.microsoft.com) para se cadastrar ou gerenciar aplicativos convergentes, referido como a experiência antiga, este guia de treinamento fará com que você comece a usar a nova experiência.

## <a name="whats-not-changing"></a>O que não está mudando?

- Seus aplicativos e configurações relacionadas podem ser encontrados como está na nova experiência. Você não precisa registrar os aplicativos novamente e os usuários de seus aplicativos não precisarão fazer login novamente.

    > [!NOTE]
    > Você deve fazer login com a conta que usou para registrar aplicativos para encontrá-los no portal Azure. Recomendamos que você verifique o usuário inscrito no portal Azure corresponde ao usuário que foi conectado ao portal de registro do Aplicativo, comparando o endereço de e-mail do seu perfil.
    >
    > Em alguns casos, especialmente quando você faz login usando contas pessoais da Microsoft (por exemplo, Outlook, Live, Xbox, etc.) com um endereço de e-mail AD do Azure, descobrimos que quando você vai para o portal Azure a partir da experiência antiga, ele o inscreve em uma conta diferente com o mesmo e-mail em seu inquilino Azure AD. Se você ainda acredita que seus aplicativos estão faltando, faça login e faça login com a conta certa.

- Os aplicativos SDK ao vivo criados usando contas pessoais da Microsoft ainda não são suportados no portal Azure e continuarão a permanecer na experiência antiga em um futuro próximo.

## <a name="key-changes"></a>Principais mudanças

-   Na experiência antiga, os aplicativos eram registrados por padrão como aplicativos convergentes que suportavam todas as contas organizacionais (multilocatários) bem como contas pessoais da Microsoft. Isso não poderia ser modificado através da experiência antiga, dificultando a criação de aplicativos que suportassem apenas contas organizacionais (multilocatários ou únicas).
    A nova experiência permite que você registre aplicativos que suportam todas essas opções. [Saiba mais sobre os tipos de aplicativos](active-directory-v2-registration-portal.md).

-   Na nova experiência, se sua conta pessoal da Microsoft também estiver em um inquilino Azure AD, você verá três guias - todos os aplicativos no inquilino, aplicativos próprios no inquilino, bem como aplicativos de sua conta pessoal. Então, se você acredita que os aplicativos registrados com sua conta pessoal da Microsoft estão faltando, verifique os aplicativos da sua conta **pessoal.**

-   Na nova experiência, você pode facilmente alternar entre inquilinos navegando para o seu perfil e escolhendo o diretório de switch.

## <a name="list-of-applications"></a>Lista de aplicativos

-   A nova lista de aplicativos mostra aplicativos que foram registrados através da experiência de registros de aplicativos legados no portal Azure (aplicativos que assinam apenas em contas AD do Azure) bem como aplicativos cadastrados no [portal de registro](https://apps.dev.microsoft.com/) de aplicativos (aplicativos que assinam tanto no Azure AD quanto em contas pessoais da Microsoft).

-   A nova lista de aplicativos tem duas colunas adicionais: **Criada na** coluna e **Certificados &** coluna segredos que mostra o status (atual, expirando em breve ou expirado) das credenciais que foram registradas no aplicativo.

## <a name="new-app-registration"></a>Registro de novo aplicativo

Na experiência antiga, para registrar um aplicativo convergente você só era obrigado a fornecer um Nome. Os aplicativos criados foram registrados como aplicativos convergentes que suportam todos os diretórios organizacionais (multilocatários) bem como contas pessoais da Microsoft.  Isso não poderia ser modificado através da experiência antiga, dificultando a criação de aplicativos que suportassem apenas contas organizacionais (multilocatários ou únicas). [Saiba mais sobre os tipos de conta suportadas](v2-supported-account-types.md)

Na nova experiência, você deve fornecer um Nome para o aplicativo e escolher os tipos de conta suportadas. Você pode fornecer opcionalmente um URI de redirecionamento.
Se você fornecer um URI redirecionado, você precisará especificar se ele é web/público (nativo/móvel e desktop). Para obter mais informações sobre como registrar um aplicativo usando a experiência de registro de novos aplicativos, consulte [este quickstart](quickstart-register-app.md).

## <a name="app-management-page"></a>Página de gerenciamento de aplicativos

A experiência antiga tinha uma única página de gerenciamento de aplicativos para aplicativos convergentes com as seguintes seções: Propriedades, segredos de aplicativos, plataformas, proprietários, permissões de gráficos microsoft, perfil e opções avançadas.

A nova experiência no portal Azure representa esses recursos em páginas separadas. Aqui é onde você pode encontrar a funcionalidade equivalente:

-   Propriedades - O Nome e o ID do aplicativo estão na página Visão Geral.

-   Segredos de Inscrição está na página de segredos & Certificados

-   A configuração das plataformas está na página Autenticação

-   As permissões do Microsoft Graph estão na página de permissões da API, juntamente com outras permissões

-   O perfil está na página branding

-   Opção avançada - O suporte ao SDK ao vivo está na página autenticação.

## <a name="application-secretscertificates--secrets"></a>Segredos de aplicativos/Certificados & segredos

Na nova experiência, os **segredos do aplicativo** foram renomeados para **Certificados & segredos**. Além disso, **as chaves públicas** são referidas como **Certificados** e **Senhas** são referidas como **segredos do Cliente**. Optamos por não trazer essa funcionalidade junto na nova experiência por razões de segurança, portanto, você não pode mais gerar um novo par de chaves.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plataformas/Autenticação: Responder URLs/uris de redirecionamento
Na experiência antiga, um aplicativo tinha a seção Plataformas para Web, nativa e API da Web para configurar URLs de redirecionamento, URL de logout e fluxo implícito.

Na nova experiência, urls de resposta\'podem ser encontrados em uma seção de Autenticação do aplicativo. Além disso, eles são chamados de URIs de redirecionamento e o formato para URIs de redirecionamento foi alterado. Eles são obrigados a estar associados a um tipo de aplicativo (web ou cliente público - móvel e desktop). [Saiba mais](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

As APIs da Web estão configuradas na página Expor uma API.

> [!NOTE]
> Experimente a nova experiência de configurações de Autenticação, na qual você pode definir configurações para seu aplicativo com base na plataforma ou no dispositivo que você deseja ter como destino. [Saiba mais](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Permissões/Permissões de API do Microsoft Graph

-   Ao selecionar uma API na experiência antiga, você pode escolher apenas entre as APIs do Microsoft Graph. Na nova experiência, você pode escolher entre muitas APIs da Microsoft, incluindo o Microsoft Graph, APIs da sua organização e suas APIs, isso é apresentado em três guias: APIs da Microsoft, APIs que minha organização usa ou Minhas APIs. A barra de pesquisa em APIs minha organização usa pesquisas de guias através de diretores de serviço no inquilino.

    > [!NOTE]
    > Você não verá esta guia se sua aplicação não estiver associada a um inquilino. Para obter mais informações sobre como solicitar permissões usando a nova experiência, consulte [este quickstart](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   A experiência antiga não tinha um botão **de permissões Grant.** Na nova experiência, há uma seção de consentimento do Grant com um botão de consentimento de **admin Grant** na seção de permissões de API de um aplicativo. Apenas um admin pode conceder consentimento e este botão está habilitado apenas para admins. Quando um admin seleciona o botão de **consentimento do admin Grant,** o consentimento do admin é concedido a todas as permissões solicitadas.

## <a name="profile"></a>Perfil
Na experiência antiga, o Profile tinha logotipo, URL de página inicial, URL de Termos de Serviço e configuração de URL de Declaração de Privacidade. Na nova experiência, elas podem ser encontradas na página Branding.

## <a name="application-manifest"></a>Manifesto do aplicativo
Na nova experiência, a página Manifest permite editar e atualizar os atributos do aplicativo. Para obter mais informações, veja [Manifesto do aplicativo](reference-app-manifest.md).

## <a name="new-ui"></a>Nova UI
Há uma nova ui para propriedades que antes só podiam ser definidas usando o editor de manifesto ou a API, ou não existiam.

-   O fluxo de concessão implícito (oauth2AllowImplicitFlow) pode ser encontrado na página Autenticação. Ao contrário da experiência antiga, você pode habilitar tokens de acesso ou tokens de ID, ou ambos.

-   Os escopos definidos por esta API (oauth2Permissions) e aplicativos clienteautorizados (pré-AuthorizedApplications) podem ser configurados através da página Expor uma API. Para obter mais informações sobre como configurar um aplicativo para ser uma API web e expor permissões/escopos, consulte [este quickstart](quickstart-configure-app-expose-web-apis.md).

-   O domínio do publisher (que é exibido aos usuários no [prompt de consentimento do aplicativo)\'](application-consent-experience.md)pode ser encontrado na página da lâmina branding. Para obter mais informações sobre como configurar um domínio de editor, consulte [este como fazer](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

-   A nova experiência ainda não suporta registros de aplicativos para inquilinos Azure AD B2C.

-   A nova experiência ainda não suporta aplicativos Live SDK criados com contas pessoais da Microsoft.

-   A alteração do valor para contas suportadas não é suportada na ui. Você precisa usar o manifesto\'do aplicativo, a menos que você esteja alternando entre o Azure AD e o multi-inquilino.

   > [!NOTE]
   > Se você é um usuário pessoal da conta microsoft no inquilino Azure AD, e o administrador do inquilino tem acesso restrito ao portal Azure, você pode ter um acesso negado. No entanto, se você passar pelo atalho digitando registros de aplicativos na barra de pesquisa ou prendendo-o, você poderá acessar a nova experiência.
