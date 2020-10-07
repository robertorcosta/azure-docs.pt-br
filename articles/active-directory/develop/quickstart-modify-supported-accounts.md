---
title: 'Início Rápido: Modificar as contas de aplicativo da plataforma de identidade da Microsoft | Azure'
description: Neste início rápido, você configura um aplicativo registrado na plataforma de identidade da Microsoft para alterar quem (ou quais contas) pode acessar o aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91258006"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Início Rápido: Modificar as contas que têm suporte de um aplicativo

Ao registrar um aplicativo na Microsoft Identity Platform, talvez você queira que ele seja acessado apenas por usuários da sua organização. Como alternativa, você talvez queira também que ele possa ser acessado por usuários em organizações externas, ou por estes e também por usuários que não fazem necessariamente parte de uma organização (contas pessoais).

Neste início rápido, você aprenderá a modificar a configuração do aplicativo para alterar quem, ou que contas, pode acessá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Entrar no portal do Azure e selecionar o aplicativo

Antes de configurar o aplicativo, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** ou a página de registro principal.
1. Siga as etapas para [alterar o registro do aplicativo para dar suporte a contas diferentes](#change-the-application-registration-to-support-different-accounts).
1. Se você tem um aplicativo de página única, [habilite a concessão implícita do OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alterar o registro do aplicativo para dar suporte a contas diferentes

Se você estiver escrevendo um aplicativo que queira disponibilizar aos seus clientes ou parceiros fora da sua organização, você precisará atualizar a definição de aplicativo no portal do Azure.

> [!IMPORTANT]
> O Azure AD exige que o URI da ID do Aplicativo de aplicativos multilocatário seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD. Por exemplo, se o nome do locatário fosse contoso.onmicrosoft.com, um URI da ID do aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`. Se o locatário tivesse um domínio verificado de contoso.com, então, um URI da ID do aplicativo válido também seria `https://contoso.com/myapp`. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

### <a name="to-change-who-can-access-your-application"></a>Para alterar quem pode acessar seu aplicativo

1. Na página **Visão Geral** do aplicativo, selecione a seção **Autenticação** e altere o valor selecionado em **Tipos de conta com suporte**.
    * Selecione **Contas somente neste diretório** se você está criando um aplicativo de LOB (linha de negócios). Essa opção não estará disponível se o aplicativo não estiver registrado em um diretório.
    * Selecione **Contas em qualquer diretório organizacional** se você deseja direcionar todos os clientes comerciais e educacionais.
    * Selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** a fim de direcionar para o conjunto mais amplo de clientes.
1. Clique em **Salvar**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitar a concessão implícita do OAuth 2.0 para aplicativos de página única

Os SPAs (aplicativos de página única) normalmente são estruturados com um front-end que usa muito JavaScript e é executado no navegador, que chama o back-end da API Web do aplicativo para executar sua lógica de negócios. Para SPAs hospedados no Azure AD, use a Concessão Implícita do OAuth 2.0 para autenticar o usuário com o Azure AD e obter um token que possa ser usado para proteger chamadas do cliente JavaScript do aplicativo para sua API Web de back-end.

Depois que o usuário tiver dado consentimento, esse mesmo protocolo de autenticação poderá ser usado para obter tokens para proteger chamadas entre o cliente e outros recursos de API Web configurados para o aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, veja o fluxo de concessão implícita do OAuth 2.0 no Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) e [v2.0](v2-oauth2-implicit-grant-flow.md).

Por padrão, a concessão implícita do OAuth 2.0 está desabilitada para aplicativos. Você pode habilitar a concessão implícita do OAuth 2.0 para seu aplicativo seguindo as etapas descritas abaixo.

### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar a Concessão Implícita do OAuth 2.0

1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** ou a página de registro principal.
1. Na página **Visão Geral** do aplicativo, selecione a seção **Autenticação**.
1. Em **Configurações avançadas**, localize a seção **Concessão implícita**.
1. Selecione **Tokens de ID**, **Tokens de acesso** ou ambos.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md)
