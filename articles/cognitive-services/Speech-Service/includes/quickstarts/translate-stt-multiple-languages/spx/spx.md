---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806385"
---
## <a name="run-the-speech-cli"></a>Executar a CLI de Fala

Agora você está pronto para executar a CLI de Fala para traduzir a fala em texto em dois idiomas diferentes.

Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e digite:

```bash
spx translate --microphone --target de-DE --target es-MX
```

A CLI de Fala traduzirá o idioma natural falado em inglês para texto impresso em alemão e espanhol (mexicano).
Pressione ENTER para interromper a ferramenta.

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source ja-JP` para reconhecer a fala em japonês.
