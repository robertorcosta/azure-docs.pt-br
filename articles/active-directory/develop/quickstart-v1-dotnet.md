---
title: Conectar usuários e chamar as APIs do Microsoft Graph com a área de trabalho do .NET (WPF) | Azure
description: Saiba como criar um aplicativo da área de trabalho .NET do Windows que se integre ao Azure AD para entrada e que chame as APIs protegidas do Azure AD usando o OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c35e44dff321c4a2f568855dacd827001a57f64a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703806"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Início Rápido: Conectar usuários e chamar a API do Microsoft Graph de um aplicativo da área de trabalho .NET (WPF)

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, tais como o Microsoft Graph ou APIs que os desenvolvedores criaram.

A [MSAL (Biblioteca de Autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma de identidade da Microsoft para acessar APIs Web seguras. A ADAL (Biblioteca de Autenticação do Active Directory) é integrada ao ponto de extremidade do Azure AD para desenvolvedores (v1.0), enquanto a MSAL é integrada ao ponto de extremidade da plataforma de identidade da Microsoft (v2.0).

Para novos aplicativos de área de trabalho, recomendamos que você use a plataforma de identidade da Microsoft (v2.0) e a MSAL para adquirir tokens e acessar APIs Web protegidas: [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows](quickstart-v2-windows-desktop.md)
