---
title: Agentes de autorização e como habilitá-los | Azure
description: Conheça os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que seu aplicativo Android use e como habilitá-los.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085324"
---
# <a name="authorization-agents-android"></a>Agentes de autorização (Android)

Este artigo descreve os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que seu aplicativo use e como habilitá-los.

Escolher uma estratégia específica para os agentes de autorização é opcional e representa aplicativos adicionais de funcionalidade que podem personalizar. A maioria dos aplicativos usará os padrões MSAL (consulte [Entender o arquivo de configuração do Android MSAL](msal-configuration.md) para ver os vários padrões).

A MSAL suporta `WebView`a autorização usando um , ou o navegador do sistema.  A imagem abaixo mostra como `WebView`ela se parece usando o , ou o navegador do sistema com CustomTabs ou sem PersonalTabs:

![Exemplos de login do MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicações de login único

Por padrão, os aplicativos integrados ao MSAL usam as Guias Personalizadas do navegador do sistema para autorizar. Ao contrário do WebViews, as Guias Personalizadas compartilham um jarro de cookies com o navegador padrão do sistema, permitindo menos logins com a Web ou outros aplicativos nativos que tenham integrado com guias personalizadas.

Se o aplicativo `WebView` usar uma estratégia sem integrar o suporte ao Microsoft Authenticator ou ao Portal da Empresa em seu aplicativo, os usuários não terão uma experiência de SSO (Single Sign On) em todo o dispositivo ou entre aplicativos nativos e aplicativos web.

Se o aplicativo usar o MSAL com suporte ao Microsoft Authenticator ou ao Portal da Empresa, os usuários poderão ter uma experiência de SSO em aplicativos se o usuário tiver um login ativo com um dos aplicativos.

## <a name="webview"></a>Exibição da Web

Para usar o WebView no aplicativo, coloque a seguinte linha na configuração do aplicativo JSON que é passada para msal:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao usar o `WebView`aplicativo, o usuário entra diretamente no aplicativo. Os tokens são mantidos dentro da caixa de areia do aplicativo e não estão disponíveis fora do pote de cookies do aplicativo. Como resultado, o usuário não pode ter uma experiência de SSO em aplicativos, a menos que os aplicativos se integrem ao Autenticador ou portal da empresa.

No `WebView` entanto, fornece a capacidade de personalizar o visual para a ui de login. Consulte [o Android WebViews](https://developer.android.com/reference/android/webkit/WebView) para saber mais sobre como fazer essa personalização.

## <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais guias personalizadas

Por padrão, o MSAL usa o navegador e uma estratégia [de guias personalizadas.](https://developer.chrome.com/multidevice/android/customtabs) Você pode indicar explicitamente essa estratégia para evitar `DEFAULT` alterações em versões futuras usando a seguinte configuração JSON no arquivo de configuração personalizado:

```json
"authorization_user_agent" : "BROWSER"
```

Use esta abordagem para fornecer uma experiência SSO através do navegador do dispositivo. A MSAL usa um pote de cookie compartilhado, que permite que outros aplicativos nativos ou aplicativos web alcancem SSO no dispositivo usando o cookie de sessão persistente definido pela MSAL.

## <a name="browser-selection-heuristic"></a>Heurística de seleção de navegador

Como é impossível para a MSAL especificar o pacote exato do navegador para usar em cada um dos amplos telefones Android, a MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO entre dispositivos.

A MSAL recupera a lista completa de navegadores instalados no dispositivo para selecionar qual navegador usar. A lista está no pedido devolvido pelo gerenciador de pacotes, o que reflete indiretamente as preferências do usuário. Por exemplo, o navegador padrão, se definido, é a primeira entrada na lista. O _primeiro_ navegador da lista será escolhido independentemente de suportar guias personalizadas. Se o navegador suportar guias personalizadas, a MSAL iniciará as Guias Personalizadas. `WebView` Guias personalizadas darão uma olhada e se sentirão mais próximas de um aplicativo e permitirão personalização básica da interface do usuário. Consulte [Guias personalizadas no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver pacotes de navegador no dispositivo, `WebView`o MSAL usará o in-app .

A ordem dos navegadores na lista do navegador é determinada pelo sistema operacional. É em ordem da maioria preferida para menos. Se a configuração padrão do dispositivo não for alterada, o mesmo navegador deve ser lançado para cada login para garantir uma experiência SSO.

> [!NOTE]
> O MSAL nem sempre prefere o Chrome se outro navegador for definido como padrão. Por exemplo, em um dispositivo que tem o Chrome e outro navegador pré-instalado, o MSAL usará o navegador que o usuário definiu como padrão.

### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver `"redirect_uri"` se eles redirecionam corretamente para o especificado no arquivo de configuração:

| | Navegador integrado | Chrome | Opera  | Microsoft Edge | Navegador UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passagem | passagem |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | passar* | passagem | passagem | passagem | falha |passagem |
| Huawei (API 26) |passar** | passagem | falha | passagem | passagem |passagem |
| Vivo (API 26) |passagem|passagem|passagem|passagem|passagem|falha|
| Pixel 2 (API 26) |passagem | passagem | passagem | passagem | falha |passagem |
| Oppo | passagem | não aplicável*** |não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passagem | passagem | passagem | passagem | falha |passagem |
| Nexus (API 28) |passagem | passagem | passagem | passagem | falha |passagem |
|MI | passagem | passagem | passagem | passagem | falha |passagem |

*O navegador embutido da Samsung é a Internet Samsung.  
**O navegador embutido da Huawei é o Huawei Browser.  
O navegador padrão não pode ser alterado dentro da configuração do dispositivo Oppo.
