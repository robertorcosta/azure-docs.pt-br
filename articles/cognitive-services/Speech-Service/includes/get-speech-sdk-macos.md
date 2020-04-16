---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399950"
---
Ao desenvolver para macOS, existem três SDKs de fala disponíveis.

- O Objective-C Speech SDK está disponível nativamente como um pacote de CacauPod
- O .NET Speech SDK pode ser usado com **xamarin.Mac** à medida que implementa o .NET Standard 2.0
- O Python Speech SDK está disponível como um módulo PyPI

> [!TIP]
> Para obter detalhes usando o Objective-C Speech SDK with Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importing Objective-C em Swift <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="system-requirements"></a>Requisitos do sistema

- Uma versão macOS 10.13 ou posterior

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        O pacote macOS CocoaPod está disponível para download e uso com o <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> ambiente de desenvolvimento integrado (IDE). Primeiro, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">baixe o CacauBin <span class="docon docon-navigate-external x-hidden-focus"> </span>binário </a>. Extrair o pod no mesmo diretório para seu uso pretendido, `pod` criar `target`um *Arquivo Pod* e listar o como um .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        O Xamarin.Mac expõe o SDK completo do macOS para desenvolvedores do .NET para compilar aplicativos do Mac nativos usando o C#. Para obter mais informações, consulte <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">código-fonte do macOS Speech SDK quickstart Objective-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK quickstart Código fonte Swift<span class="docon docon-navigate-external x-hidden-focus"></span></a>