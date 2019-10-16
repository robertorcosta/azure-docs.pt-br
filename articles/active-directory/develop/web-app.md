---
title: Aplicativos Web no Azure Active Directory
description: Descreve quais são os aplicativos Web e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d41cd23d551e4834bf6b94f513e36ff46c1cd45
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373654"
---
# <a name="web-apps"></a>Aplicativos Web

Os aplicativos Web são aplicativos que autenticam um usuário em um navegador da Web para um aplicativo Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para que ele entre no Azure AD. O Azure AD retorna uma resposta de logon por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Este cenário oferece suporte ao logon usando os protocolos WS-Federation, SAML 2.0 e OpenID Connect.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação do navegador para aplicativo Web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

1. Quando um usuário visita o aplicativo e precisa fazer logon, eles são redirecionados por meio de uma solicitação de logon para o ponto de extremidade de autenticação no Azure AD.
1. O usuário faz logon na página de logon.
1. Se a autenticação for bem-sucedida, o Azure AD criará um token de autenticação e retornará uma resposta de conexão para a URL de Resposta do aplicativo que foi configurada no portal do Azure. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são exigidas pelo aplicativo para validar o token.
1. O aplicativo valida o token usando uma chave de assinatura pública e informações do emissor disponíveis no documento de metadados de federação para o Azure AD. Depois que o aplicativo valida o token, ele inicia uma nova sessão com o usuário. Esta sessão permite que o usuário acesse o aplicativo até expirar.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código do navegador da Web para cenários de aplicativo Web. E volte sempre, pois adicionamos novos exemplos com frequência.

## <a name="app-registration"></a>Registro do aplicativo

Para registrar um aplicativo Web, consulte [registrar um aplicativo](quickstart-register-app.md).

* Locatário Único – Se você estiver criando um aplicativo apenas para a sua organização, ele deverá ser registrado no diretório da sua empresa usando o portal do Azure.
* Multilocatário – Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deverá ser registrado no diretório da sua empresa, mas também deverá ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que os permita ter autorização para seu aplicativo. Ao se inscreverem para seu aplicativo, uma caixa de diálogo será apresentada, mostrando as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização talvez precise dar consentimento. Quando o usuário ou administrador der seu consentimento, o aplicativo é registrado em seu diretório.

## <a name="token-expiration"></a>Expiração do token

A sessão do usuário expira quando a vida útil do token emitido pelo Azure AD expira. Seu aplicativo pode encurtar esse período de tempo, se desejado, por exemplo, desconectando usuários com base em um período de inatividade. Quando a sessão expirar, o usuário deverá fazer logon novamente.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [Tipos de aplicativo e cenários](app-types.md)
* Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
