---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399890"
---
:::row:::
    :::column span="3":::
        O JavaScript Speech SDK está disponível como um pacote NPM, consulte <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitivaservices-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e ele complementa o repositório GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitiva-Services-Speech-SDK-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o JavaScript Speech SDK esteja disponível como um pacote NPM, portanto ambos os navegadores da Web do cliente e o Node. js podem consumir isso, considere as várias implicações de arquitetura de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de objeto de documento ( <span class="docon docon-navigate-external x-hidden-focus"></span> dom)</a> não está disponível para aplicativos do lado do servidor, assim como o <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> sistema de arquivos</a> não está disponível para aplicativos do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Gerenciador de pacotes do node. js (NPM)

Para instalar o SDK de fala do JavaScript, execute `npm install` o seguinte comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Marca de script HTML

Como alternativa, você poderia incluir diretamente uma `<script>` marca no `<head>` elemento HTMLs, contando com o <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM sindicate <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Para obter mais informações, consulte o guia de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">início rápido <span class="docon docon-navigate-external x-hidden-focus"> </span>do SDK de fala do navegador da Web </a>.
