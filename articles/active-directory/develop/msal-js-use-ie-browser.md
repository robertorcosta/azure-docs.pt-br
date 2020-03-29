---
title: Problemas no Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Use a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com navegador Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695850"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemas conhecidos nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

A Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) é gerada para [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) para que ele possa ser executado no Internet Explorer. Há, no entanto, algumas coisas para saber.

## <a name="run-an-app-in-internet-explorer"></a>Execute um aplicativo no Internet Explorer
Se você pretende usar MSAL.js em aplicativos que podem ser executados no Internet Explorer, você precisará adicionar uma referência a um polígrafo de promessa antes de fazer referência ao script MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Isso porque o Internet Explorer não suporta promessas JavaScript nativamente.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Depuração de um aplicativo em execução no Internet Explorer

### <a name="running-in-production"></a>Correndo na produção
Implantar seu aplicativo para produção (por exemplo, em aplicativos Web Do Azure) normalmente funciona bem, desde que o usuário final tenha aceitado popups. Testamos com o Internet Explorer 11.

### <a name="running-locally"></a>Funcionando localmente
Se você deseja executar e depurar localmente seu aplicativo em execução no Internet Explorer, você precisa *http://localhost:1234*estar ciente das seguintes considerações (suponha que você deseja executar seu aplicativo como ):

- O Internet Explorer tem um mecanismo de segurança chamado "modo protegido", que impede que o MSAL.js opere corretamente. Entre os sintomas, após o login, a http://localhost:1234/nullpágina pode ser redirecionada para .

- Para executar e depurar seu aplicativo localmente, você precisará desativar este "modo protegido". Para isso:

    1. Clique em Ferramentas **do** Internet Explorer (o ícone de engrenagem).
    1. Selecione **Opções de Internet** e, em seguida, a guia **Segurança.**
    1. Clique na região da **Internet** e desfaça a verificação **do Modo de Proteção (requer reiniciar o Internet Explorer)**. O Internet Explorer avisa que seu computador não está mais protegido. Clique em **OK**.
    1. Reinicie o Internet Explorer.
    1. Execute e depura sua aplicação.

Quando terminar, restaure as configurações de segurança do Internet Explorer.  Selecione **Configurações** -> **Opções de** -> **segurança da Internet** -> **Redefinir todas as regiões para o nível padrão**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [problemas conhecidos ao usar O MSAL.js no Internet Explorer](msal-js-use-ie-browser.md).