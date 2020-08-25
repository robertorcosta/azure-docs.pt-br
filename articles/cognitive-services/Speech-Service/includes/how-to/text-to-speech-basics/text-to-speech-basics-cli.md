---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170106"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Sintetizar uma fala em um alto-falante

Agora você está pronto para executar a CLI de Fala para sintetizar a fala de um texto. Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala. Em seguida, execute o comando a seguir.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

A CLI de Fala produzirá idioma natural em inglês por meio do alto-falante do computador.

## <a name="synthesize-speech-to-a-file"></a>Sintetizar fala em um arquivo

Execute o comando a seguir para alterar a saída do seu alto-falante para um arquivo `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A CLI de Fala produzirá idioma natural em inglês no arquivo de áudio `greetings.wav`.
No Windows, você pode reproduzir o arquivo de áudio digitando `start greetings.wav`.