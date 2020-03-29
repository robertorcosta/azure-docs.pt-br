---
title: Problemas no Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas ao usar a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com navegadores Internet Explorer e Microsoft Edge.
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
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696088"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemas conhecidos nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemas devido a zonas de segurança
Tivemos vários relatos de problemas com autenticação no IE e no Microsoft Edge (desde a atualização da versão do *navegador Microsoft Edge para 40.15063.0.0).* Estamos rastreando isso e informamos a equipe do Microsoft Edge. Enquanto o Microsoft Edge trabalha em uma resolução, aqui está uma descrição dos problemas que ocorrem com frequência e as possíveis soluções alternativas que podem ser implementadas.

### <a name="cause"></a>Causa
A causa para a maioria dessas questões é a seguinte. O armazenamento de sessão e o armazenamento local são particionados por zonas de segurança no navegador Microsoft Edge. Nesta versão específica do Microsoft Edge, quando o aplicativo é redirecionado para as regiões, o armazenamento da sessão e o armazenamento local são liberados. Especificamente, o armazenamento da sessão é liberado na navegação regular do navegador, e tanto a sessão quanto o armazenamento local são liberados no modo InPrivate do navegador. O MSAL.js salva determinado estado no armazenamento da sessão e depende da verificação deste estado durante os fluxos de autenticação. Quando o armazenamento da sessão é liberado, este estado é perdido e, portanto, resulta em experiências quebradas.

### <a name="issues"></a>Problemas

- **Loops de redirecionamento infinito e recargas de página durante a autenticação**. Quando os usuários fazem login no aplicativo no Microsoft Edge, eles são redirecionados para trás da página de login AAD e ficam presos em um loop de redirecionamento infinito, resultando em recargas repetidas da página. Isso geralmente é acompanhado `invalid_state` por um erro no armazenamento da sessão.

- **Adquira loops de token infinitos e erro AADSTS50058**. Quando um aplicativo em execução no Microsoft Edge tenta adquirir um token para um recurso, o aplicativo pode ficar preso em um loop infinito da chamada de token de aquisição, juntamente com o seguinte erro do AAD em seu rastreamento de rede:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **A janela pop-up não fecha ou fica presa ao usar o login através do Popup para autenticar**. Ao autenticar através da janela popup no Microsoft Edge ou IE (InPrivate), depois de inserir credenciais e fazer login, se vários domínios entre as zonas de segurança estiverem envolvidos na navegação, a janela popup não fecha porque o MSAL.js perde a alça para a janela pop-up.  

### <a name="update-fix-available-in-msaljs-023"></a>Atualização: Correção disponível em MSAL.js 0.2.3
Correções para os problemas de loop de redirecionamento de autenticação foram lançadas em [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Habilite `storeAuthStateInCookie` a bandeira na configuração MSAL.js para aproveitar esta correção. Por padrão, este sinalizador é definido como falso.

Quando `storeAuthStateInCookie` o sinalizador estiver ativado, o MSAL.js usará os cookies do navegador para armazenar o estado de solicitação necessário para validação dos fluxos auth.

> [!NOTE]
> Esta correção ainda não está disponível para os invólucros msal-angular e msal-angularjs. Esta correção não resolve o problema com janelas Popup.

Use as soluçãos abaixo.

#### <a name="other-workarounds"></a>Outras soluçãos
Certifique-se de testar que seu problema está ocorrendo apenas na versão específica do navegador Microsoft Edge e funciona nos outros navegadores antes de adotar essas soluçãos.  
1. Como um primeiro passo para contornar esses problemas, certifique-se de que o domínio do aplicativo e quaisquer outros sites envolvidos nos redirecionamentos do fluxo de autenticação sejam adicionados como sites confiáveis nas configurações de segurança do navegador, de modo que eles pertençam à mesma zona de segurança.
Para fazer isso, siga estas etapas:
    - Abra o **Internet Explorer** e clique nas **configurações** (ícone de engrenagem) no canto superior direito
    - Selecione **opções de Internet**
    - Selecione a guia **Segurança**
    - Na opção **Sites confiáveis,** clique no botão **sites** e adicione as URLs na caixa de diálogo que abre.

2. Como mencionado anteriormente, uma vez que apenas o armazenamento da sessão é liberado durante a navegação regular, você pode configurar o MSAL.js para usar o armazenamento local. Isso pode ser `cacheLocation` definido como parâmetro de configuração ao inicializar o MSAL.

Observe que isso não resolverá o problema para navegação inPrivate, uma vez que tanto a sessão quanto o armazenamento local são limpos.

## <a name="issues-due-to-popup-blockers"></a>Problemas devido a bloqueadores pop-up

Há casos em que popups são bloqueados no IE ou no Microsoft Edge, por exemplo, quando um segundo pop-up ocorre durante a autenticação multifatorial. Você receberá um alerta no navegador para permitir o pop-up uma vez ou sempre. Se você optar por permitir, o navegador abrirá `null` a janela pop-up automaticamente e devolverá uma alça para ela. Como resultado, a biblioteca não tem uma alça para a janela e não há como fechar a janela pop-up. O mesmo problema não acontece no Chrome quando ele solicita que você permita popups porque ele não abre automaticamente uma janela pop-up.

Como **solução para a solução,** os desenvolvedores precisarão permitir popups no IE e no Microsoft Edge antes de começar a usar seu aplicativo para evitar esse problema.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [o uso do MSAL.js no Internet Explorer](msal-js-use-ie-browser.md).
