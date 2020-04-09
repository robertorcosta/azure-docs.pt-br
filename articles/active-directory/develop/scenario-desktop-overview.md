---
title: Construa um aplicativo de desktop que chame APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de desktop que chama APIs da Web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882993"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicativo de desktop que chama APIs da Web

Aprenda tudo o que você precisa para criar um aplicativo de desktop que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introdução

Se você ainda não tiver, crie seu primeiro aplicativo seguindo o quickstart da área de trabalho .NET, o quickstart da Universal Windows Platform (UWP) ou o aplicativo nativo do macOS:

> [!div class="nextstepaction"]
> [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quickstart: adquira um token e chame a API do Microsoft Graph a partir de um aplicativo UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quickstart: adquira um token e chame a API do Microsoft Graph a partir de um aplicativo nativo do macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Visão geral

Você escreve um aplicativo de desktop e deseja fazer login com os usuários em seu aplicativo e chamar APIs da Web, como o Microsoft Graph, outras APIs da Microsoft ou sua própria API web. Você tem várias possibilidades:

- Você pode usar a aquisição interativa de tokens:

  - Se o aplicativo de desktop suportar controles gráficos, por exemplo, se for um aplicativo Windows.Form, um aplicativo WPF ou um aplicativo nativo do macOS.
  - Ou, se for um aplicativo .NET Core e você concordar em ter a interação de autenticação com o Azure Active Directory (Azure AD) acontecer no navegador do sistema.

- Para aplicativos hospedados no Windows, também é possível que aplicativos em execução em computadores unidos a um domínio do Windows ou Azure AD se unam para adquirir um token silenciosamente usando autenticação integrada do Windows.
- Finalmente, e embora não seja recomendado, você pode usar um nome de usuário e uma senha em aplicativos públicos de clientes. Ainda é necessário em alguns cenários como DevOps. Usá-lo impõe restrições à sua aplicação. Por exemplo, ele não pode fazer login em um usuário que precisa realizar autenticação multifatorial (acesso condicional). Além disso, sua solicitação não se beneficiará de um único login (SSO).

  Isso também vai contra os princípios da autenticação moderna e só é oferecido por ser herdado.

  ![Aplicativo de desktop](media/scenarios/desktop-app.svg)

- Se você escrever uma ferramenta de linha de comando portátil, provavelmente um aplicativo .NET Core que é executado no Linux ou Mac, e se você aceitar que a autenticação será delegada ao navegador do sistema, você pode usar autenticação interativa. O .NET Core não fornece um [navegador da Web,](https://aka.ms/msal-net-uses-web-browser)então a autenticação acontece no navegador do sistema. Caso contrário, a melhor opção nesse caso é usar o fluxo de código do dispositivo. Esse fluxo também é usado para aplicativos sem navegador, como aplicativos IoT.

  ![Aplicativo sem navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificidades

Os aplicativos de desktop têm uma série de especificidades. Eles dependem principalmente se seu aplicativo usa autenticação interativa ou não.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo para desktop: Registro de aplicativos](scenario-desktop-app-registration.md)
