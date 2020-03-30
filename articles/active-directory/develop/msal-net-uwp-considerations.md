---
title: Considerações da UWP (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar a Universal Windows Platform (UWP) com a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132521"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerações para usar a Plataforma Universal Windows com MSAL.NET
Os desenvolvedores de aplicativos que usam a Universal Windows Platform (UWP) com MSAL.NET devem considerar os conceitos que este artigo apresenta.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma Windows Runtime (WinRT), `PublicClientApplication` tem `UseCorporateNetwork`a propriedade Boolean . Essa propriedade permite que aplicativos do Windows 8.1 e aplicativos UWP se beneficiem da autenticação integrada do Windows (IWA) se o usuário estiver conectado a uma conta que tenha um inquilino federado do Azure Active Directory (Azure AD). Os usuários que estão logados no sistema operacional também podem usar o soo de assinatura de um único login. Quando você `UseCorporateNetwork` define a propriedade, MSAL.NET usa um corretor de autenticação web (WAB).

> [!IMPORTANT]
> Definir `UseCorporateNetwork` a propriedade como true assume que o desenvolvedor do aplicativo habilitou o IWA no aplicativo. Para habilitar o IWA:
> - Na guia Recursos do `Package.appxmanifest`aplicativo UWP, **ative** os seguintes recursos:
>   - **Autenticação de Empresa**
>   - **Redes Privadas (Cliente e Servidor)**
>   - **Certificado de usuário compartilhado**

O IWA não é habilitado por padrão porque o Microsoft Store requer um alto nível de verificação antes de aceitar aplicativos que solicitam os recursos de autenticação corporativa ou certificados de usuário compartilhados. Nem todos os desenvolvedores querem fazer esse nível de verificação.

Na plataforma UWP, a implementação do WAB subjacente não funciona corretamente em cenários corporativos onde o acesso condicional é habilitado. Os usuários veem sintomas desse problema quando tentam fazer login usando o Windows Hello. Quando o usuário é solicitado a escolher um certificado:

- O certificado para o PIN não foi encontrado.
- Depois que o usuário escolhe um certificado, eles não são solicitados para o PIN.

Você pode tentar evitar esse problema usando um método alternativo, como senha de usuário e autenticação do telefone, mas a experiência não é boa.

## <a name="troubleshooting"></a>Solução de problemas

Alguns clientes relataram o seguinte erro de login em ambientes corporativos específicos em que sabem que têm conexão com a internet e que a conexão funciona com uma rede pública.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Você pode evitar esse problema certificando-se de que o WAB (o componente subjacente do Windows) permita uma rede privada. Você pode fazer isso definindo uma chave de registro:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para obter mais informações, consulte [o corretor de autenticação da Web - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Próximas etapas
As amostras a seguir fornecem mais informações.

Amostra | Plataforma | Descrição 
|------ | -------- | -----------|
|[ativo-diretório-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Um aplicativo cliente UWP que usa MSAL.NET. Ele acessa o Microsoft Graph para um usuário que autentica usando um ponto final do Azure AD 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[ativo-diretório-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um simples aplicativo Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e a Azure AD através do ponto final do Azure AD 2.0. Ele também mostra como acessar o Microsoft Graph e mostra o token resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
