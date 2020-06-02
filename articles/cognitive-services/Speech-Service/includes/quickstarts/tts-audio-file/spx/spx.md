---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806260"
---
## <a name="run-the-speech-cli"></a>Executar a CLI de Fala

Agora você está pronto para executar a CLI de Fala para sintetizar a fala de um texto em um novo arquivo de áudio.

Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e digite:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A CLI de Fala produzirá idioma natural em inglês no arquivo de áudio `greetings.wav`.
No Windows, você pode reproduzir o arquivo de áudio digitando `start greetings.wav`.
