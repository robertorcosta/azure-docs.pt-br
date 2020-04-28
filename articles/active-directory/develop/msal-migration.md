---
title: Migrar para a biblioteca de autenticação da Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a MSAL (biblioteca de autenticação da Microsoft) e a ADAL (biblioteca de autenticação do Azure AD) e como migrar para o MSAL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164926"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrar aplicativos para a biblioteca de autenticação da Microsoft (MSAL)

A MSAL (biblioteca de autenticação da Microsoft) e a ADAL (biblioteca de autenticação do Azure AD) são usadas para autenticar entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhava com o Azure AD para plataforma de desenvolvedores (v1.0) a fim de autenticar identidades do Azure AD (contas corporativas e de estudante) por meio da solicitação de tokens usando a Biblioteca de Autenticação do Active Directory (ADAL). Usando MSAL:

- Você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e contas da Microsoft e contas sociais e locais por meio do Azure AD B2C), pois ele usa o ponto de extremidade da plataforma de identidade da Microsoft.
- Os usuários terão a melhor experiência de logon único.
- Seu aplicativo pode habilitar o consentimento incremental e o suporte ao acesso condicional é mais fácil.
- Você se beneficia da inovação.

**MSAL agora é a biblioteca de autenticação recomendada para usar com a plataforma de identidade da Microsoft**. Nenhum recurso novo será implementado na ADAL. Os esforços se concentram em melhorar o MSAL.

Os artigos a seguir descrevem as diferenças entre as bibliotecas MSAL e ADAL e ajudam você a migrar para o MSAL:
- [Migrar para o MSAL.NET](msal-net-migration.md)
- [Migrar para o MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrar para a MSAL.Android](migrate-android-adal-msal.md)
- [Migrar para a MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrar para a MSAL Python](migrate-python-adal-msal.md)
- [Migrar para a MSAL para Java](migrate-adal-msal-java.md)
- [Migrar aplicativos Xamarin usando agentes para o MSAL.NET](msal-net-migration-ios-broker.md)
