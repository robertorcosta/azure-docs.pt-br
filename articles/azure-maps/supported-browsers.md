---
title: Navegadores suportados pelo Web SDK | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre navegadores suportados para o Microsoft Azure Maps Web SDK e como verificar se um navegador é um navegador suportado.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988780"
---
# <a name="web-sdk-supported-browsers"></a>Navegadores compatíveis com o SDK Web

O Azure Maps Web SDK fornece uma função de ajudante chamada [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Esta função detecta se um navegador da Web tem o conjunto mínimo de recursos WebGL necessários para suportar o carregamento e a renderização do controle do mapa. Aqui está um exemplo de como usar a função:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

O Azure Maps Web SDK suporta os seguintes navegadores de desktop:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (Mac OS X) (versão atual e anterior)

Veja também [segmentar navegadores legados](#Target-Legacy-Browsers) mais tarde neste artigo.

## <a name="mobile"></a>Celular

O Azure Maps Web SDK suporta os seguintes navegadores móveis:

- Android
  - Versão atual do Chrome no Android 6.0 e posterior
  - Chrome WebView no Android 6.0 e posterior
- iOS
  - Safari móvel na versão principal atual e anterior do iOS
  - UIWebView e WKWebView na versão atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se você estiver incorporando um mapa dentro de um aplicativo móvel usando um controle WebView, você pode preferir usar o [pacote npm do Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) em vez de fazer referência à versão do SDK hospedada no Azure Content Delivery Network. Essa abordagem reduz o tempo de carregamento porque o SDK já está no dispositivo do usuário e não precisa ser baixado em tempo de execução.

## <a name="nodejs"></a>Node.js

Os seguintes módulos Web SDK também são suportados no Node.js:

- Módulo de serviços (módulo de[documentação](how-to-use-services-module.md) | [npm)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Segmentar navegadores legados

Você pode querer atingir navegadores mais antigos que não suportam WebGL ou que têm apenas suporte limitado para ele. Nesses casos, recomendamos que você use os serviços do Azure Maps juntamente com um controle de mapa de código aberto como [o Folheto](https://leafletjs.com/). Aqui está um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas do Azure + Folheto" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps +</a> Leaflet<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Web SDK do Azure Maps:

> [!div class="nextstepaction"]
> [Controle de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
