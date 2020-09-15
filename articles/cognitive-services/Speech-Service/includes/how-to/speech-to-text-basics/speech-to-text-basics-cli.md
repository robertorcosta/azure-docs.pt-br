---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570565"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Conversão de fala em texto de um microfone

Conecte e ligue o microfone do seu PC e desative todos os aplicativos que também possam usar o microfone. Alguns computadores têm um microfone interno, enquanto outros exigem a configuração de um dispositivo Bluetooth.

Agora você está pronto para executar a CLI de Fala para reconhecer a fala do microfone. Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e execute o comando a seguir.

```bash
spx recognize --microphone
```

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

Fale próximo ao microfone e você verá a transcrição de suas palavras em texto em tempo real. A CLI de Fala será interrompida após um período de silêncio ou quando você pressionar CTRL-C.

## <a name="speech-to-text-from-audio-file"></a>Conversão de fala em texto de um arquivo de áudio

A CLI de Fala pode reconhecer a fala em muitos formatos de arquivo e idiomas naturais. Neste exemplo, você pode usar um arquivo WAV (16kHz ou 8kHz, de 16 bits e PCM mono) que contenha fala em inglês. Ou, se quiser um exemplo rápido, baixe o arquivo <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> e copie-o para o mesmo diretório que o arquivo binário da CLI de Fala.

Você está pronto para executar a CLI de Fala para reconhecer a fala encontrada no arquivo de áudio executando o comando a seguir.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

A CLI de Fala mostrará uma transcrição do texto da fala na tela.