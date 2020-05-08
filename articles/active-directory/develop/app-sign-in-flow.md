---
title: Fluxo de entrada do aplicativo com a plataforma de identidade da Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o fluxo de entrada de aplicativos Web, de área de trabalho e móveis na plataforma Microsoft Identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584344"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Fluxo de entrada do aplicativo com a plataforma de identidade da Microsoft

Este tópico discute o fluxo de entrada básico para aplicativos Web, desktop e móveis usando a plataforma de identidade da Microsoft. Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre cenários de entrada com suporte pela plataforma de identidade da Microsoft.

## <a name="web-app-sign-in-flow"></a>Fluxo de entrada do aplicativo Web

Quando um usuário navega no navegador para um aplicativo Web, acontece o seguinte:

* O aplicativo Web determina se o usuário está autenticado.
* Se o usuário não for autenticado, o aplicativo Web delegará ao Azure AD para conectar o usuário. Essa entrada será compatível com a política da organização, o que pode significar solicitar que o usuário insira suas credenciais, usando a autenticação multifator ou não usando uma senha (por exemplo, usando o Windows Hello).
* O usuário será solicitado a consentir o acesso de que o aplicativo cliente precisa. É por isso que os aplicativos cliente precisam ser registrados com o Azure AD, para que a plataforma de identidade da Microsoft possa fornecer tokens que representem o acesso ao qual o usuário tenha consentido.

Quando o usuário foi autenticado com êxito:

* A plataforma de identidade da Microsoft envia um token para o aplicativo Web.
* Um cookie é salvo, associado ao domínio do AD do Azure, que contém a identidade do usuário no jar do cookie do navegador. Na próxima vez que um aplicativo usar o navegador para navegar até o ponto de extremidade de autorização da plataforma de identidade da Microsoft, o navegador apresentará o cookie para que o usuário não precise entrar novamente. Isso também é a maneira como o SSO é obtido. O cookie é produzido pelo Azure AD e só pode ser compreendido pelo Azure AD.
* Em seguida, o aplicativo Web valida o token. Se a validação for realizada com sucesso, o aplicativo Web exibirá a página protegida e salvará um cookie de sessão no jar do cookie do navegador. Quando o usuário navega para outra página, o aplicativo Web sabe que o usuário é autenticado com base no cookie de sessão.

O diagrama de sequência a seguir resume essa interação:

![processo de autenticação do aplicativo Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como um aplicativo Web determina se o usuário é autenticado

Os desenvolvedores de aplicativos Web podem indicar se todas ou apenas determinadas páginas exigem autenticação. Por exemplo, no ASP.NET/ASP.NET Core, isso é feito adicionando o `[Authorize]` atributo às ações do controlador.

Esse atributo faz com que o ASP.NET Verifique a presença de um cookie de sessão que contém a identidade do usuário. Se um cookie não estiver presente, o ASP.NET redirecionará a autenticação para o provedor de identidade especificado. Se o provedor de identidade for o Azure AD, o aplicativo Web redireciona a autenticação `https://login.microsoftonline.com`para o, que exibe uma caixa de diálogo de entrada.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Como um aplicativo Web delega a entrada à plataforma Microsoft Identity e Obtém um token

A autenticação do usuário ocorre por meio do navegador. O protocolo OpenID usa mensagens de protocolo HTTP padrão.

* O aplicativo Web envia um HTTP 302 (redirecionamento) para o navegador para usar a plataforma de identidade da Microsoft.
* Quando o usuário é autenticado, a plataforma de identidade da Microsoft envia o token para o aplicativo Web usando um redirecionamento por meio do navegador.
* O redirecionamento é fornecido pelo aplicativo Web na forma de um URI de redirecionamento. Esse URI de redirecionamento é registrado com o objeto de aplicativo do Azure AD. Pode haver vários URIs de redirecionamento porque o aplicativo pode ser implantado em várias URLs. Portanto, o aplicativo Web também precisará especificar o URI de redirecionamento a ser usado.
* O Azure AD verifica se o URI de redirecionamento enviado pelo aplicativo Web é um dos URIs de redirecionamento registrados para o aplicativo.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Fluxo de entrada do aplicativo móvel e desktop

O fluxo descrito acima se aplica, com pequenas diferenças, a desktops e aplicativos móveis.

Aplicativos móveis e de desktop podem usar um controle da Web inserido ou um navegador do sistema para autenticação. O diagrama a seguir mostra como um desktop ou aplicativo móvel usa a MSAL (biblioteca de autenticação da Microsoft) para adquirir tokens de acesso e chamar APIs da Web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa um navegador para obter tokens. Assim como acontece com os aplicativos Web, a autenticação é delegada à plataforma Microsoft Identity.

Como o Azure AD salva o mesmo cookie de identidade no navegador, como ele faz para aplicativos Web, se o aplicativo nativo ou móvel usar o navegador do sistema, ele receberá imediatamente o SSO com o aplicativo Web correspondente.

Por padrão, o MSAL usa o navegador do sistema. A exceção é .NET Framework aplicativos de área de trabalho em que um controle incorporado é usado para fornecer uma experiência de usuário mais integrada.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos que abrangem noções básicas de autenticação e autorização:

* Consulte [autenticação vs. autorização](authentication-vs-authorization.md) para saber mais sobre os conceitos básicos de autenticação e autorização na plataforma Microsoft Identity.
* Consulte [tokens de segurança](security-tokens.md) para saber como tokens de acesso, tokens de atualização e tokens de ID são usados em autenticação e autorização.
* Consulte [modelo de aplicativo](application-model.md) para saber mais sobre o processo de registro de seu aplicativo para que ele possa ser integrado à plataforma de identidade da Microsoft.

Para saber mais sobre o fluxo de entrada do aplicativo:

* Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre outros cenários de autenticação de usuários com suporte na plataforma de identidade da Microsoft.
* Consulte [bibliotecas do MSAL](msal-overview.md) para saber mais sobre as bibliotecas da Microsoft que ajudam você a desenvolver aplicativos que funcionam com contas da Microsoft, contas do Azure AD e Azure ad B2C usuários em um modelo de programação único e simplificado.
