---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806496"
---
## <a name="find-a-file-that-contains-speech"></a>Localizar um arquivo que contenha fala

A CLI de Fala pode reconhecer a fala em muitos formatos de arquivo e idiomas naturais. Para este guia de início rápido, você pode usar um arquivo WAV (16kHz ou 8kHz, de 16 bits e PCM mono) que contenha fala em inglês.

1. Baixe o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>.
2. Copie o arquivo `whatstheweatherlike.wav` para o mesmo diretório que o arquivo binário da CLI de Fala.

## <a name="run-the-speech-cli"></a>Executar a CLI de Fala

Agora você está pronto para executar a CLI de Fala para reconhecer a fala encontrada no arquivo de som.

Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e digite:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

A CLI de Fala mostrará uma transcrição do texto da fala na tela. Em seguida, a CLI de Fala será fechada.
