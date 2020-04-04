---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656492"
---
:::row:::
    :::column span="3":::
        O Speech SDK suporta versões windows 10 e Windows Server 2016, ou versões posteriores. Versões anteriores **não** são suportadas oficialmente. É possível usar partes do Speech SDK com versões anteriores do Windows, embora não seja aconselhável.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisitos do sistema

O Speech SDK no Windows requer o <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ <span class="docon docon-navigate-external x-hidden-focus"></span> Redistributable para o Visual Studio 2019</a> no sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalar para x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalar para x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalar para ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Para a entrada do microfone, as bibliotecas do Media Foundation precisam ser instaladas. Essas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK de Fala sem essas bibliotecas, contanto que o microfone não seja usado como o dispositivo de entrada de áudio.

Os arquivos necessários do SDK de Fala podem ser implantados no mesmo diretório do seu aplicativo. Dessa forma, seu aplicativo pode acessar diretamente as bibliotecas. Certifique-se de selecionar a versão correta (x86/x64) que corresponde à sua aplicação.

| Nome                                            | Função                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK principal, necessário para implantação nativa e gerenciada |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerenciada                      |

> [!NOTE]
> A começar pela versão 1.3.0 o arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no SDK principal.

> [!IMPORTANT]
> Para o projeto C# do Aplicativo de formulários do Windows (.NET Framework), certifique-se de que as bibliotecas estão incluídas nas configurações de implantação do projeto. Você pode verificar `Properties -> Publish Section`isso em . Clique `Application Files` no botão e encontre bibliotecas correspondentes na lista de rolagem para baixo. Certifique-se de que `Included`o valor está definido como . O Visual Studio incluirá o arquivo quando o projeto for publicado/implantado.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
