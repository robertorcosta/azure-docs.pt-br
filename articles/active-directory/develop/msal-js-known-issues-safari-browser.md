---
title: Problemas conhecidos do navegador Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre problemas ao usar a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com navegador Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696105"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemas conhecidos no navegador Safari com MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação silenciosa do token no Safari 12 e ITP 2.0

Os sistemas operacionais Apple iOS 12 e MacOS 10.14 incluíram uma versão do [navegador Safari 12](https://developer.apple.com/safari/whats-new/). Para fins de segurança e privacidade, o Safari 12 inclui o [Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Isso essencialmente faz com que o navegador solte cookies de terceiros sendo definidos. O ITP 2.0 também trata os cookies definidos pelos provedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto no MSAL.js

O MSAL.js usa um Iframe oculto para realizar `acquireTokenSilent` a aquisição e renovação silenciosa de tokens como parte das chamadas. As solicitações de token silenciosas dependem do Iframe ter acesso à sessão de usuário autenticada representada pelos cookies definidos pelo Azure AD. Com o ITP 2.0 impedindo o acesso a esses cookies, o MSAL.js `acquireTokenSilent` falha em adquirir e renovar silenciosamente tokens e isso resulta em falhas.

Não há solução para este problema neste momento e estamos avaliando opções com a comunidade de padrões.

### <a name="work-around"></a>Trabalhe em torno de

Por padrão, a configuração ITP está ativada no navegador Safari. Você pode desativar essa configuração navegando para privacidade de preferências**e** **desmarcando** -> a opção **Impedir de rastrear o site.**

![configuração safári](./media/msal-js-known-issue-safari-browser/safari.png)

Você precisará lidar `acquireTokenSilent` com as falhas com uma chamada de token de aquisição interativa, que solicita ao usuário que faça login.
Para evitar logins repetidos, uma abordagem que `acquireTokenSilent` você pode implementar é lidar com a falha e fornecer ao usuário uma opção para desativar a configuração itp no Safari antes de prosseguir com a chamada interativa. Uma vez que a configuração seja desativada, as renovações silenciosas subseqüentes do token devem ser bem sucedidas.
