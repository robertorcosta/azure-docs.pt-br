---
title: Migrar para a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre a Microsoft Authentication Library (MSAL) e a Azure AD Authentication Library (ADAL) e como migrar para o MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164926"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrar aplicativos para a Microsoft Authentication Library (MSAL)

Tanto a Microsoft Authentication Library (MSAL) quanto a Azure AD Authentication Library (ADAL) são usadas para autenticar entidades Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhava com o Azure AD para plataforma de desenvolvedores (v1.0) a fim de autenticar identidades do Azure AD (contas corporativas e de estudante) por meio da solicitação de tokens usando a Biblioteca de Autenticação do Active Directory (ADAL). Usando o MSAL:

- Você pode autenticar um conjunto mais amplo de identidades microsoft (identidades Azure AD e contas Microsoft, e contas sociais e locais através do Azure AD B2C) à medida que usa o ponto final da plataforma de identidade Microsoft.
- Seus usuários terão a melhor experiência de cada sinal único.
- Seu aplicativo pode permitir o consentimento incremental e o suporte ao Acesso Condicional é mais fácil.
- Você se beneficia da inovação.

**A MSAL é agora a biblioteca auth recomendada para usar com a plataforma de identidade Microsoft**. Nenhum novo recursos será implementado no ADAL. Os esforços estão focados na melhoria do MSAL.

Os seguintes artigos descrevem as diferenças entre as bibliotecas MSAL e ADAL e ajudam você a migrar para o MSAL:
- [Migrar para o MSAL.NET](msal-net-migration.md)
- [Migrar para o MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrar para a MSAL.Android](migrate-android-adal-msal.md)
- [Migrar para MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrar para a MSAL Python](migrate-python-adal-msal.md)
- [Migrar para a MSAL para Java](migrate-adal-msal-java.md)
- [Migrar aplicativos Xamarin usando agentes para o MSAL.NET](msal-net-migration-ios-broker.md)
