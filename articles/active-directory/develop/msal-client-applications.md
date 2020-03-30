---
title: Aplicativos públicos e confidenciais (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Conheça os aplicativos públicos do cliente e do cliente confidencial na Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084125"
---
# <a name="public-client-and-confidential-client-applications"></a>Aplicativos públicos de clientes e clientes confidenciais
A Microsoft Authentication Library (MSAL) define dois tipos de clientes: clientes públicos e clientes confidenciais. Os dois tipos de clientes se distinguem por sua capacidade de autenticar com segurança com o servidor de autorização e manter a confidencialidade de suas credenciais de cliente. Em contraste, a Azure AD Authentication Library (ADAL) usa o que é chamado de contexto de *autenticação* (que é uma conexão com o Azure AD).

- **Aplicativos de clientes confidenciais** são aplicativos que são executados em servidores (aplicativos web, aplicativos de API web ou até mesmo aplicativos de serviço/daemon). Eles são considerados de difícil acesso, e por essa razão capazes de manter um aplicativo em segredo. Clientes confidenciais podem guardar segredos de tempo de configuração. Cada instância do cliente tem uma configuração distinta (incluindo id do cliente e segredo do cliente). Esses valores são difíceis para os usuários finais extrairem. Um aplicativo web é o cliente confidencial mais comum. O ID do cliente é exposto através do navegador da web, mas o segredo é passado apenas no canal de trás e nunca exposto diretamente.

    Aplicativos confidenciais do cliente: <BR>
    ![Web app](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![Daemon/service](media/msal-client-applications/daemon-service.png)

- **Aplicativos de clientes públicos** são aplicativos que são executados em dispositivos ou computadores desktop ou em um navegador da Web. Eles não são confiáveis para manter com segurança segredos de aplicativos, então eles só acessam APIs da Web em nome do usuário. (Eles suportam apenas fluxos públicos de clientes.) Clientes públicos não podem guardar segredos de tempo de configuração, então eles não têm segredos de clientes.

    Aplicativos públicos de clientes: <BR>
    ![Aplicativo](media/msal-client-applications/desktop-app.png) ![de desktop](media/msal-client-applications/browserless-app.png) ![API Mobile sem navegador](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> No MSAL.js, não há separação de aplicativos públicos e confidenciais.  O MSAL.js representa os aplicativos clientes como aplicativos baseados em agentes de usuário, clientes públicos nos quais o código do cliente é executado em um agente de usuário como um navegador da Web. Esses clientes não armazenam segredos porque o contexto do navegador é abertamente acessível.

## <a name="comparing-the-client-types"></a>Comparando os tipos de clientes
Aqui estão algumas semelhanças e diferenças entre cliente público e aplicativos de clientes confidenciais:

- Ambos os tipos de aplicativos mantêm um cache de token do usuário e podem adquirir um token silenciosamente (quando o token já está no cache do token). Os aplicativos confidenciais do cliente também têm um cache de tokens de aplicativo para tokens que são para o próprio aplicativo.
- Ambos os tipos de aplicativos gerenciam contas de usuário e podem obter uma conta do cache do token do usuário, obter uma conta de seu identificador ou remover uma conta.
- Os aplicativos de clientes públicos têm quatro maneiras de adquirir um token (quatro fluxos de autenticação). Os aplicativos confidenciais do cliente têm três maneiras de adquirir um token (e uma maneira de calcular a URL do provedor de identidade autorizar o endpoint). Para obter mais informações, consulte [Adquirir tokens](msal-acquire-cache-tokens.md).

Se você já usou o ADAL, você pode notar que, ao contrário do contexto de autenticação da ADAL, no MSAL o ID do cliente (também chamado *de ID* do aplicativo ou *ID do aplicativo)* é aprovado uma vez na construção do aplicativo. Ele não precisa ser aprovado novamente quando o aplicativo adquire um token. Isso é verdade tanto para aplicativos públicos quanto confidenciais. Construtores de aplicativos confidenciais de clientes também são passadas credenciais de clientes: o segredo que compartilham com o provedor de identidade.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:
- [Opções de configuração de aplicativos do cliente](msal-client-application-configuration.md)
- [Instantivar aplicativos clientes usando MSAL.NET](msal-net-initializing-client-applications.md)
- [Instanciar aplicativos clientes usando MSAL.js](msal-js-initializing-client-applications.md)
