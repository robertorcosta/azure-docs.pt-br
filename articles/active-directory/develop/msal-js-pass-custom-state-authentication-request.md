---
title: Passe o estado personalizado em solicitações de autenticação (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a passar um valor de parâmetro de estado personalizado na solicitação de autenticação usando a Biblioteca de Autenticação microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084944"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passe o estado personalizado em solicitações de autenticação usando MSAL.js

O parâmetro *de estado,* conforme definido por OAuth 2.0, está incluído em uma solicitação de autenticação e também é devolvido na resposta de token para evitar ataques de falsificação de solicitação entre sites. Por padrão, a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) passa um valor de parâmetro de *estado* único gerado aleatoriamente nas solicitações de autenticação.

O parâmetro de estado também pode ser usado para codificar informações do estado do aplicativo antes de redirecionar. Você pode passar o estado do usuário no aplicativo, como a página ou exibição em que eles estavam, como entrada para este parâmetro. A biblioteca MSAL.js permite que você passe seu `Request` estado personalizado como parâmetro de estado no objeto:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Se você quiser pular um token armazenado em cache e ir `forceRefresh` para o servidor, passe no booleano para o objeto AuthenticationParameters usado para fazer uma solicitação de login/token.
> `forceRefresh`não deve ser usado por padrão, devido ao impacto de desempenho em sua aplicação.
> Contar com o cache dará aos seus usuários uma melhor experiência.
> Pular o cache só deve ser usado em cenários onde você sabe que os dados armazenados atualmente em cache não têm informações atualizadas.
> Como uma ferramenta de admin que adiciona funções a um usuário que precisa obter um novo token com funções atualizadas.

Por exemplo: 

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O aprovado no estado é anexado ao guid único definido pelo MSAL.js ao enviar a solicitação. Quando a resposta é devolvida, o MSAL.js verifica uma partida de `Response` estado `accountState`e, em seguida, retorna o costume passado no estado no objeto como .

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Para saber mais, leia sobre [a construção de um aplicativo de uma página única (SPA)](scenario-spa-overview.md) usando o MSAL.js.