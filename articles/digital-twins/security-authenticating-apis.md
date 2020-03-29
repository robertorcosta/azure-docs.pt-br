---
title: Entenda a autenticação da API - Azure Digital Twins | Microsoft Docs
description: Aprenda a se conectar e autenticar com APIs usando a Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512990"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Conecte-se e autentique com APIs

O Gêmeos Digitais do Azure usa o Active Directory do Azure (Azure AD) para autenticar usuários e proteger aplicativos. O Microsoft Azure Active Directory oferece suporte à autenticação para diversas arquiteturas modernas. Todos eles são baseados nos protocolos padrão da indústria OAuth 2.0 ou OpenID Connect. Além disso, os desenvolvedores podem usar o Microsoft Azure Active Directory para criar aplicativos de locatário único e de linha de negócios (LOB). Os desenvolvedores também podem usar o Azure AD para desenvolver [aplicativos multilocatários.](how-to-multitenant-applications.md)

Para obter uma visão geral do Microsoft Azure Active Directory, visite a [página de fundamentos](https://docs.microsoft.com/azure/active-directory/fundamentals/) para obter guias passo a passo, conceitos e iniciações rápidas.

> [!TIP]
> Siga o [Tutorial](tutorial-facilities-setup.md) para configurar e executar um aplicativo de exemplo Azure Digital Twins.

Para integrar um aplicativo ou serviço ao Azure AD, um desenvolvedor deve primeiro registrar o aplicativo no Azure AD. Para obter instruções detalhadas e capturas de tela, leia [este quickstart](../active-directory/develop/quickstart-register-app.md).

[Cinco cenários de aplicativos principais](../active-directory/develop/v2-app-types.md) são suportados pelo Microsoft Azure Active Directory:

* Aplicativo de uma única página (SPA): um usuário precisa entrar em um aplicativo de uma única página protegido pelo Microsoft Azure Active Directory.
* Navegador da Web para aplicativo da Web: um usuário precisa entrar em um aplicativo da Web protegido pelo Azure Active Directory.
* Aplicativo nativo para API da web: um aplicativo nativo que é executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API da web protegida pelo Microsoft Azure Active Directory.
* Aplicativo Web para API Web: um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD.
* Daemon ou aplicativo de servidor para API da web: um aplicativo daemon ou um aplicativo de servidor sem interface da web precisa obter recursos de uma API da web protegida pela Microsoft Azure AD.

> [!IMPORTANT]
> O Azure Digital Twins suporta as duas bibliotecas de autenticação a seguir:
> * A mais recente [Biblioteca de Autenticação da Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Biblioteca de Autenticação de Diretório Ativo do Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chame os Gêmeos Digitais do Azure de uma API da web de camada intermediária

Quando os desenvolvedores arquitetam os Gêmeos Digitais do Azure, eles geralmente criam um aplicativo ou API de camada intermediária. O aplicativo ou API chama o downstream dos Gêmeos Digitais do Azure. Para dar suporte a essa arquitetura da solução da web padrão, certifique-se de que os usuários primeiro:

1. Autentiquem com o aplicativo de camada intermediária

1. Um token em nome do OAuth 2.0 é adquirido durante a autenticação

1. O token adquirido é então usado para autenticar com ou chamar as APIs que são downstream ainda mais usando o fluxo Em nome de

Para obter instruções sobre como orquestrar o fluxo em nome do fluxo, leia [OAuth 2.0 On-Behalf-Of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Você também pode exibir exemplos de código em [chamar uma API da web downstream](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Próximas etapas

Para configurar e testar os Gêmeos Digitais do Azure usando o fluxo de concessão implícita do OAuth 2.0, leia [Configurar o Postman](./how-to-configure-postman.md).

Para saber mais sobre a segurança dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
