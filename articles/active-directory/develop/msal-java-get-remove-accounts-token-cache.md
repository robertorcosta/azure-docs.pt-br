---
title: Obtenha & remover contas do cache de token (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como visualizar e remover contas do cache de token usando a Biblioteca de Autenticação da Microsoft para Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696190"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Obter e remover contas do cache de token usando MSAL para Java

O MSAL for Java fornece um cache de token na memória por padrão. O cache de token na memória dura a duração da instância do aplicativo.

## <a name="see-which-accounts-are-in-the-cache"></a>Veja quais contas estão no cache

Você pode verificar quais contas estão `PublicClientApplication.getAccounts()` no cache chamando como mostrado no exemplo a seguir:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Remover contas do cache

Para remover uma conta do cache, encontre a conta `PublicClientApplicatoin.removeAccount()` que precisa ser removida e, em seguida, ligue como mostrado no exemplo a seguir:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Saiba mais

Se você estiver usando o MSAL para Java, aprenda sobre [serialização de cache de token personalizado no MSAL for Java](msal-java-token-cache-serialization.md).
