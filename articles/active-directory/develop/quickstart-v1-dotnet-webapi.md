---
title: Criar uma API Web do .NET com o Azure AD para autenticação e autorização | Microsoft Docs
description: Como compilar uma API Web do .NET MVC que se integre ao Azure AD para autenticação e autorização.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b080ce3869bcdbbde9724a33433e5f0659b37e4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703823"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Início Rápido: Criar uma API Web do .NET que se integre ao Azure AD para autenticação e autorização

A [plataforma de identidade da Microsoft](v2-overview.md) é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtêm tokens para chamar APIs da Microsoft, tais como o Microsoft Graph ou APIs que os desenvolvedores criaram.

A [MSAL (Biblioteca de Autenticação da Microsoft)](msal-overview.md) permite que os desenvolvedores adquiram tokens do ponto de extremidade da plataforma de identidade da Microsoft para acessar APIs Web seguras. A ADAL (Biblioteca de Autenticação do Active Directory) é integrada ao ponto de extremidade do Azure AD para desenvolvedores (v1.0), enquanto a MSAL é integrada ao ponto de extremidade da plataforma de identidade da Microsoft (v2.0).

Para novas APIs Web, recomendamos que você use a plataforma de identidade da Microsoft (v2.0) e a MSAL para adquirir tokens e acessar APIs Web seguras: [Início Rápido: adicionar entrada com a Microsoft para um aplicativo Web ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
