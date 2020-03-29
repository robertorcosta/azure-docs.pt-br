---
title: Desenvolver aplicativos com o Speech SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como implantar um aplicativo que usa o Speech SDK em plataformas suportadas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330797"
---
# <a name="ship-an-application"></a>Enviar um aplicativo

Observe a [Licença do SDK de fala](https://aka.ms/csspeech/license201809), bem como as [notificações de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) ao distribuir o SDK de Fala de Serviços Cognitivos do Azure. Além disso, leia a [Política de privacidade da Microsoft](https://aka.ms/csspeech/privacy).

Dependendo da plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK dos Serviços Cognitivos de Fala é testado no Windows 10 e no Windows Server 2016.

O Cognitive Services Speech SDK requer o [Microsoft Visual C++ Redistributable para o Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Você pode baixar instaladores para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019`:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se seu aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior será necessário no computador de destino.

Para a entrada do microfone, as bibliotecas do Media Foundation precisam ser instaladas. Essas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK de Fala sem essas bibliotecas, contanto que o microfone não seja usado como o dispositivo de entrada de áudio.

Os arquivos necessários do SDK de Fala podem ser implantados no mesmo diretório do seu aplicativo. Dessa forma, seu aplicativo pode acessar diretamente as bibliotecas. Selecione a versão correta (Win32/x64) que corresponda ao seu aplicativo.

| Nome | Função |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK principal, necessário para implantação nativa e gerenciada |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerenciada                      |

> [!NOTE]
> A começar pela versão 1.3.0 o arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no SDK principal.

> [!NOTE]
> Para o projeto C# do Aplicativo de formulários do Windows (.NET Framework), certifique-se de que as bibliotecas estão incluídas nas configurações de implantação do projeto. Você pode verificar `Properties -> Publish Section`isso em . Clique `Application Files` no botão e encontre bibliotecas correspondentes na lista de rolagem para baixo. Certifique-se de que `Included`o valor está definido como . O Visual Studio incluirá o arquivo quando o projeto for publicado/implantado.

## <a name="linux"></a>Linux

O Speech SDK atualmente suporta as distribuições Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8.
Para um aplicativo nativo, você precisa enviar a biblioteca do SDK de Fala, `libMicrosoft.CognitiveServices.Speech.core.so`.
Selecione a versão (x86/x64) que corresponde ao seu aplicativo. Dependendo da versão do Linux, talvez você também precise incluir as seguintes dependências:

- As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
- A biblioteca OpenSSL `libssl.so.1.0.2`(ou`libssl.so.1.0.0` )
- A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

No Ubuntu, as bibliotecas de GNU C já devem estar instaladas por padrão. Os três últimos podem ser instalados usando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9 instale esses pacotes:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Em RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções [sobre como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
