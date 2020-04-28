---
title: Agentes de autorização e como habilitá-los | Azure
description: Saiba mais sobre os diferentes agentes de autorização que a MSAL (biblioteca de autenticação da Microsoft) permite que seu aplicativo Android use e como habilitá-los.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77085324"
---
# <a name="authorization-agents-android"></a>Agentes de autorização (Android)

Este artigo descreve os diferentes agentes de autorização que a MSAL (biblioteca de autenticação da Microsoft) permite que seu aplicativo use e como habilitá-los.

A escolha de uma estratégia específica para agentes de autorização é opcional e representa a funcionalidade adicional que os aplicativos podem personalizar. A maioria dos aplicativos usará os padrões MSAL (consulte [entender o arquivo de configuração do MSAL do Android](msal-configuration.md) para ver os vários padrões).

O MSAL dá suporte à `WebView`autorização usando um ou o navegador do sistema.  A imagem abaixo mostra como ela está usando o `WebView`ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de logon do MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicações de logon único

Por padrão, os aplicativos integrados ao MSAL usam as guias personalizadas do navegador do sistema para autorizar. Ao contrário das exibições, as guias personalizadas compartilham um jar de cookie com o navegador do sistema padrão, permitindo menos entradas com a Web ou outros aplicativos nativos que se integraram com guias personalizadas.

Se o aplicativo usar uma `WebView` estratégia sem integrar Microsoft Authenticator ou portal da empresa suporte em seu aplicativo, os usuários não terão uma experiência de logon único (SSO) em todo o dispositivo ou entre aplicativos nativos e aplicativos Web.

Se o aplicativo usar MSAL com suporte a Microsoft Authenticator ou Portal da Empresa, os usuários poderão ter uma experiência de SSO entre aplicativos se o usuário tiver uma entrada ativa com um dos aplicativos.

## <a name="webview"></a>Exibição da Web

Para usar o WebView no aplicativo, coloque a seguinte linha no JSON de configuração do aplicativo que é passado para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao usar o no aplicativo `WebView`, o usuário entra diretamente no aplicativo. Os tokens são mantidos dentro da área restrita do aplicativo e não estão disponíveis fora do jar do cookie do aplicativo. Como resultado, o usuário não pode ter uma experiência de SSO entre aplicativos, a menos que os aplicativos se integrem com o autenticador ou Portal da Empresa.

No entanto, `WebView` o fornece a capacidade de personalizar a aparência da interface do usuário de entrada. Consulte [Webviews do Android](https://developer.android.com/reference/android/webkit/WebView) para obter mais informações sobre como fazer essa personalização.

## <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais guias personalizadas

Por padrão, o MSAL usa o navegador e uma estratégia de [guias personalizadas](https://developer.chrome.com/multidevice/android/customtabs) . Você pode indicar explicitamente essa estratégia para evitar alterações em versões `DEFAULT` futuras no usando a seguinte configuração de JSON no arquivo de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Use essa abordagem para fornecer uma experiência de SSO por meio do navegador do dispositivo. O MSAL usa um jar de cookie compartilhado, que permite que outros aplicativos nativos ou aplicativos Web obtenham o SSO no dispositivo usando o cookie de sessão persistente definido por MSAL.

## <a name="browser-selection-heuristic"></a>Heurística de seleção do navegador

Como é impossível para MSAL especificar o pacote exato do navegador a ser usado em cada uma das grandes matrizes de telefones Android, o MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO entre dispositivos.

MSAL recupera a lista completa de navegadores instalados no dispositivo para selecionar qual navegador usar. A lista está na ordem retornada pelo Gerenciador de pacotes, que reflete indiretamente as preferências do usuário. Por exemplo, o navegador padrão, se definido, é a primeira entrada na lista. O _primeiro_ navegador na lista será escolhido, independentemente de ele dar suporte a guias personalizadas. Se o navegador oferecer suporte a guias personalizadas, o MSAL iniciará a guia personalizada. as guias personalizadas terão uma aparência mais próxima de um `WebView` aplicativo e permitirão a personalização básica da interface do usuário. Consulte [guias personalizadas no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver nenhum pacote de navegador no dispositivo, o MSAL usará o no `WebView`aplicativo.

A ordem dos navegadores na lista de navegadores é determinada pelo sistema operacional. Ele é, na ordem, mais preferencial para o mínimo. Se a configuração padrão do dispositivo não for alterada, o mesmo navegador deverá ser iniciado para cada conexão para garantir uma experiência de SSO.

> [!NOTE]
> MSAL não prefere mais o Chrome se outro navegador estiver definido como padrão. Por exemplo, em um dispositivo que tem o Chrome e outro navegador pré-instalado, o MSAL usará o navegador que o usuário definiu como o padrão.

### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver se redirecionados corretamente `"redirect_uri"` para o especificado no arquivo de configuração:

| | Navegador interno | Chrome | Opera  | Microsoft Edge | Navegador de UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passagem | passagem |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | passá | passagem | passagem | passagem | falha |passagem |
| Huawei (API 26) |Pass * * | passagem | falha | passagem | passagem |passagem |
| Vivo (API 26) |passagem|passagem|passagem|passagem|passagem|falha|
| Pixel 2 (API 26) |passagem | passagem | passagem | passagem | falha |passagem |
| Oppo | passagem | Não aplicável * * * |não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passagem | passagem | passagem | passagem | falha |passagem |
| Nexus (API 28) |passagem | passagem | passagem | passagem | falha |passagem |
|MI | passagem | passagem | passagem | passagem | falha |passagem |

* O navegador interno da Samsung é a Samsung Internet.  
* * O navegador interno do Huawei é o navegador Huawei.  
O navegador padrão não pode ser alterado dentro da configuração do dispositivo Oppo.
