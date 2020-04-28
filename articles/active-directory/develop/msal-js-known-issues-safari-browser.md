---
title: Problemas conhecidos do navegador Safari (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas conhecidos ao usar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com o navegador Safari.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696105"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemas conhecidos no navegador Safari com MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação de token silenciosa no Safari 12 e ITP 2,0

Os sistemas operacionais Apple iOS 12 e MacOS 10,14 incluíam uma versão do [navegador Safari 12](https://developer.apple.com/safari/whats-new/). Para fins de segurança e privacidade, o Safari 12 inclui a [prevenção de rastreamento inteligente 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Essencialmente, isso faz com que o navegador descartar os cookies de terceiros que estão sendo definidos. O ITP 2,0 também trata os cookies definidos por provedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto no MSAL. js

O MSAL. js usa um iframe oculto para executar a aquisição e a renovação de tokens `acquireTokenSilent` silenciosas como parte das chamadas. As solicitações de token silenciosas dependem do iframe que tem acesso à sessão de usuário autenticado representada pelos cookies definidos pelo Azure AD. Com o ITP 2,0 impedindo o acesso a esses cookies, o MSAL. js falha ao adquirir e renovar silenciosamente tokens e isso resulta em `acquireTokenSilent` falhas.

Não há nenhuma solução para esse problema neste ponto e estamos avaliando opções com a comunidade de padrões.

### <a name="work-around"></a>Solução alternativa

Por padrão, a configuração ITP está habilitada no navegador Safari. Você pode desabilitar essa configuração navegando até **preferências** -> **privacidade** e desmarcando a opção **impedir rastreamento entre sites** .

![configuração do Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Você precisará lidar com as `acquireTokenSilent` falhas com uma chamada de token de aquisição interativa, que solicita que o usuário entre.
Para evitar entradas repetidas, uma abordagem que você pode implementar é manipular a `acquireTokenSilent` falha e fornecer ao usuário uma opção para desabilitar a configuração ITP no Safari antes de prosseguir com a chamada interativa. Quando a configuração estiver desabilitada, as renovações de token silencioso subsequentes deverão ser realizadas com sucesso.
