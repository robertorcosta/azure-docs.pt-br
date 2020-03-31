---
title: Comportamento de solicitação interativa (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar o comportamento rápido em chamadas interativas usando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695969"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento imediato em solicitações interativas do MSAL.js

Quando um usuário tiver estabelecido uma sessão AD ativa do Azure com várias contas de usuário, o login do Azure AD, por padrão, solicitará ao usuário que selecione uma conta antes de prosseguir para fazer login. Os usuários não verão uma experiência de seleção de conta se houver apenas uma única sessão autenticada com o Azure AD.

A biblioteca MSAL.js (a partir de v0.2.4) não envia`loginRedirect`um `loginPopup` `acquireTokenRedirect` parâmetro `acquireTokenPopup`de alerta durante as solicitações interativas ( , e ), e, portanto, não impõe qualquer comportamento imediato. Para solicitações silenciosas `acquireTokenSilent` de token usando o método, o `none`MSAL.js passa um parâmetro prompt definido para .

Com base no cenário do aplicativo, você pode controlar o comportamento imediato para as solicitações interativas definindo o parâmetro prompt nos parâmetros de solicitação passados para os métodos. Por exemplo, se você quiser invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores de solicitação podem ser passados ao autenticar com o Azure AD:

**login:** Esse valor forçará o usuário a inserir credenciais na solicitação de autenticação.

**select_account:** Esse valor fornecerá ao usuário uma experiência de seleção de contas listando todas as contas em sessão.

**consentimento:** Esse valor invocará o diálogo de consentimento OAuth que permite que os usuários concedam permissões ao aplicativo.

**nenhum:** Esse valor garantirá que o usuário não veja nenhum prompt interativo. Recomenda-se não passar esse valor para métodos interativos em MSAL.js, pois pode ter comportamentos inesperados. Em vez `acquireTokenSilent` disso, use o método para alcançar chamadas silenciosas.

## <a name="next-steps"></a>Próximas etapas

Leia mais `prompt` sobre o parâmetro no protocolo [de concessão implícita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) que a biblioteca MSAL.js usa.
