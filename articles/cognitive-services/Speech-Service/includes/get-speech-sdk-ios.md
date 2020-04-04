---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656521"
---
:::row:::
    :::column span="3":::
        Ao desenvolver para iOS, existem dois SDKs de fala disponíveis. O Objective-C Speech SDK está disponível nativamente como um pacote de Cacau iOS. Alternativamente, o .NET Speech SDK pode ser usado com Xamarin.iOS à medida que implementa o .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Para obter detalhes usando o Objective-C Speech SDK with Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importing Objective-C em Swift <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="system-requirements"></a>Requisitos do sistema

- Uma versão macOS 10.3 ou posterior
- Alvo iOS 9.3 ou posterior

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        O pacote iOS CocoaPod está disponível para download e uso com o <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> ambiente de desenvolvimento integrado (IDE). Primeiro, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">baixe o CacauBin <span class="docon docon-navigate-external x-hidden-focus"> </span>binário </a>. Extrair o pod no mesmo diretório para seu uso pretendido, `pod` criar `target`um *Arquivo Pod* e listar o como um .
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS expõe o SDK de iOS completo para desenvolvedores de .NET. Compile aplicativos iOS totalmente nativos usando C# ou F# no Visual Studio. Para obter mais informações, consulte <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️. &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Código-fonte do IOS Speech SDK quickstart Objective-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">código fonte do iOS Speech SDK quickstart Swift<span class="docon docon-navigate-external x-hidden-focus"></span></a>