---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400015"
---
:::row:::
    :::column span="3":::
        O C++ Speech SDK está disponível no Windows, Linux e macOS. Para obter mais informações, consulte <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. cognitivaservices. <span class="docon docon-navigate-external x-hidden-focus"> </span>Speech </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pacote NuGet do C++

O SDK de fala do C++ pode ser instalado do **Gerenciador de pacotes** com `Install-Package` o comando a seguir.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binários e arquivos de cabeçalho do C++

Como alternativa, o SDK de fala do C++ pode ser instalado a partir de binários. Baixe o SDK como um <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">pacote <span class="docon docon-navigate-external x-hidden-focus"></span> . tar</a> e descompacte os arquivos em um diretório de sua escolha. O conteúdo deste pacote (que inclui arquivos de cabeçalho para as arquiteturas de destino x86 e x64) é estruturado da seguinte maneira:

  | Caminho                   | Descrição                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licença                                              |
  | `ThirdPartyNotices.md` | Avisos de terceiros                                  |
  | `include`              | Arquivos de cabeçalho para C++                                 |
  | `lib/x64`              | Biblioteca x64 nativa para vinculação ao seu aplicativo |
  | `lib/x86`              | Biblioteca x86 nativa para vinculação ao seu aplicativo |

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Código-fonte do Windows, Linux e macOS QuickStart C++<span class="docon docon-navigate-external x-hidden-focus"></span></a>