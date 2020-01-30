---
title: Adquirir um token e chamar o Microsoft Graph (console do .NET Core) (v1.0) | Azure
description: Crie um aplicativo daemon do .NET que se integra ao Azure AD e chama as APIs protegidas do Azure AD usando o OAuth 2.0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703789"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Início Rápido: Adquirir um token e chamar o Microsoft Graph usando uma identidade de aplicativo do console (v1.0)

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, tais como o Microsoft Graph ou APIs que os desenvolvedores criaram.

A [MSAL (Biblioteca de Autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma de identidade da Microsoft para acessar APIs Web seguras. A ADAL (Biblioteca de Autenticação do Active Directory) é integrada ao ponto de extremidade do Azure AD para desenvolvedores (v1.0), enquanto a MSAL é integrada ao ponto de extremidade da plataforma de identidade da Microsoft (v2.0).

## <a name="next-steps"></a>Próximas etapas

Para novos aplicativos daemon do .NET, recomendamos que você use a plataforma de identidade da Microsoft (v2.0) e a MSAL para adquirir tokens e acessar APIs Web protegidas: [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo de console usando uma identidade do aplicativo](quickstart-v2-netcore-daemon.md).
