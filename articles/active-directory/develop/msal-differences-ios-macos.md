---
title: MSAL para iOS & diferenças macOS | Azure
titleSuffix: Microsoft identity platform
description: Descreve as diferenças de uso da Microsoft Authentication Library (MSAL) entre iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084970"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Diferenças da Biblioteca de Autenticação da Microsoft para iOS e macOS

Este artigo explica as diferenças de funcionalidade entre a Microsoft Authentication Library (MSAL) para iOS e macOS.

> [!NOTE]
> No Mac, o MSAL só suporta aplicativos macOS.

## <a name="general-differences"></a>Diferenças gerais

MSAL para macOS é um subconjunto da funcionalidade disponível para iOS.

O MSAL para macOS não suporta:

- diferentes tipos `ASWebAuthenticationSession`de `SFAuthenticationSession` `SFSafariViewController`navegador, tais como , .
- autenticação intermediada através do aplicativo Microsoft Authenticator não é suportada para macOS.

O compartilhamento de chaveiros entre aplicativos do mesmo editor é mais limitado no macOS 10.14 e anterior. Use [listas de controle de acesso](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) para especificar os caminhos para os aplicativos que devem compartilhar o chaveiro. O usuário pode ver solicitações adicionais do chaveiro.

No macOS 10.15+, o comportamento do MSAL é o mesmo entre iOS e macOS. A MSAL usa [grupos de acesso a chaveiros](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) para compartilhamento de chaveiros. 

### <a name="conditional-access-authentication-differences"></a>Diferenças de autenticação de acesso condicional

Para cenários de Acesso Condicional, haverá menos solicitações de usuário quando você usar O MSAL para iOS. Isso porque o iOS usa o aplicativo corretor (Microsoft Authenticator) que nega a necessidade de solicitar ao usuário em alguns casos.

### <a name="project-setup-differences"></a>Diferenças de configuração do projeto

**macOS**

- Ao configurar seu projeto no macOS, certifique-se de que seu aplicativo esteja assinado com um certificado de desenvolvimento ou produção válido. O MSAL ainda funciona no modo não assinado, mas se comportará de forma diferente no que diz respeito à persistência do cache. O aplicativo só deve ser executado sem assinatura para fins de depuração. Se você distribuir o aplicativo sem assinatura, ele irá:
1. Em 10.14 e anteriormente, o MSAL solicitará ao usuário uma senha de chaveiro toda vez que reiniciar o aplicativo.
2. No 10.15+, a MSAL solicitará ao usuário credenciais para cada aquisição de token. 

- Os aplicativos macOS não precisam implementar a chamada do AppDelegate.

**iOS**

- Existem etapas adicionais para configurar seu projeto para suportar o fluxo de corretores de autenticação. Os passos são chamados no tutorial.
- Projetos de iOS precisam registrar esquemas personalizados no info.plist. Isso não é necessário no macOS.
