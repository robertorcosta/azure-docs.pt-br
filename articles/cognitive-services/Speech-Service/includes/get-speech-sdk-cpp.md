---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656691"
---
:::row:::
    :::column span="3":::
        O C++ Speech SDK está disponível no Windows, Linux e macOS. Para obter mais informações, consulte <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pacote C++ NuGet

O SDK de voz C++ pode ser instalado `Install-Package` no Gerenciador de **pacotes** com o seguinte comando.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binários C++ e arquivos de cabeçalho

Alternativamente, o SDK de fala C++ pode ser instalado a partir de binários. Baixe o SDK como um <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> pacote .tar</a> e desempacote os arquivos em um diretório de sua escolha. O conteúdo deste pacote (que inclui arquivos de cabeçalho para arquiteturas de destino x86 e x64) está estruturado da seguinte forma:

  | Caminho                   | Descrição                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licença                                              |
  | `ThirdPartyNotices.md` | Avisos de terceiros                                  |
  | `include`              | Arquivos de cabeçalho para C++                                 |
  | `lib/x64`              | Biblioteca x64 nativa para vinculação ao seu aplicativo |
  | `lib/x86`              | Biblioteca x86 nativa para vinculação ao seu aplicativo |

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Código-fonte do Windows, Linux e macOS Quickstart C++<span class="docon docon-navigate-external x-hidden-focus"></span></a>