---
title: Tipos de contas com suporte-plataforma Microsoft Identity | Azure
description: Documentação conceitual sobre públicos-alvo e tipos de conta com suporte em aplicativos
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700049"
---
# <a name="supported-account-types"></a>Tipos de conta com suporte

Este artigo explica quais tipos de contas (às vezes chamadas de públicos) têm suporte em aplicativos.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Tipos de contas com suporte em aplicativos da plataforma de identidade da Microsoft

Na nuvem pública do Microsoft Azure, a maioria dos tipos de aplicativos pode conectar usuários com qualquer público-alvo:

- Se você estiver criando um aplicativo de linha de negócios (LOB), poderá conectar usuários em sua própria organização. Esse tipo de aplicativo às vezes é chamado de **locatário único**.
- Se você for um ISV, poderá criar um aplicativo que conecte os usuários:

  - em qualquer organização. Esse aplicativo é chamado de aplicativo da Web **multilocatário**. Algumas vezes, você descobrirá que ele conecta usuários com as contas corporativas ou de estudante.
  - Com a conta corporativa ou de estudante ou com a conta Microsoft pessoal.
  - Somente com a conta Microsoft pessoal.
    > [!NOTE]
    > Atualmente, a plataforma de identidade da Microsoft dá suporte a contas Microsoft pessoais somente registrando um aplicativo para **contas corporativas ou de estudante ou contas Microsoft pessoais** e, em seguida, restringe a entrada no código do aplicativo especificando uma autoridade do Azure AD ao criar o aplicativo, como `https://login.microsoftonline.com/consumers`.

- Se você estiver inserindo uma empresa no aplicativo de consumidores, também poderá conectar usuários com suas identidades sociais, usando o Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Determinados fluxos de autenticação não dão suporte a todos os tipos de conta

Alguns tipos de conta não podem ser usados ​​com determinados fluxos de autenticação. Por exemplo, na área de trabalho, aplicativos UWP ou daemon:

- Os aplicativos daemon só podem ser usados ​​com organizações do Azure Active Directory. Não faz sentido tentar usar aplicativos daemon para manipular contas Microsoft pessoais (o consentimento do administrador nunca será dado).  
- Você só pode usar o fluxo de autenticação integrada do Windows com contas corporativa ou de estudante (em sua organização ou em qualquer organização). Na verdade, a Autenticação Integrada do Windows funciona com contas de domínio e exige que os computadores estejam ingressados no domínio ou ingressados no Azure AD. Esse fluxo não faz sentido para contas Microsoft pessoais.
- A [Concessão de Senha do Proprietário do Recurso](./v2-oauth-ropc.md) (Nome de Usuário/Senha) não pode ser usada com contas Microsoft pessoais. De fato, as contas Microsoft pessoais exigem que o usuário consinta em acessar recursos pessoais em cada sessão de entrada. E é por isso que esse comportamento não é compatível com fluxos não interativos.
- O fluxo de código do dispositivo não funciona com contas Microsoft pessoais.

## <a name="supported-account-types-in-national-clouds"></a>Tipos de conta com suporte em nuvens nacionais

 Os aplicativos também podem conectar usuários em [nuvens nacionais](authentication-national-cloud.md). No entanto, as contas Microsoft pessoais não têm suporte nessas nuvens (por definição dessas nuvens). É por isso que os tipos de conta com suporte são reduzidos, para essas nuvens, à sua organização (locatário único) ou a qualquer organização (aplicativos multilocatários).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [Locação no Azure Active Directory](./single-and-multi-tenant-apps.md)
- Saiba mais sobre [Nuvens Nacionais](./authentication-national-cloud.md)
