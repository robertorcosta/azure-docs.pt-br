---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: a79d42255a9d04f7a7c8c1be151beda1804df89b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656613"
---
:::row:::
    :::column span="3":::
        O JavaScript Speech SDK está disponível como um pacote npm, veja <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e seu companheiro GitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o JavaScript Speech SDK esteja disponível como um pacote npm, assim, tanto o Node.js quanto os navegadores da Web cliente podem consumi-lo - considere as várias implicações arquitetônicas de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de <span class="docon docon-navigate-external x-hidden-focus"></span> objeto de documento (DOM)</a> não está disponível para aplicativos do lado do servidor, assim como o <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> sistema de arquivos</a> não está disponível para aplicativos do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Para instalar o JavaScript Speech SDK, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Para obter mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js Speech SDK quickstart <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
