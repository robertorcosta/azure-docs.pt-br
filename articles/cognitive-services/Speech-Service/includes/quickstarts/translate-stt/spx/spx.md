---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806220"
---
## <a name="run-the-speech-cli"></a>Executar a CLI de Fala

Agora você está pronto para executar a CLI de Fala para traduzir a fala em texto em um idioma diferente.

Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e digite:

```bash
spx translate --microphone --target de-DE
```

A CLI de Fala traduzirá o idioma natural falado em inglês para texto impresso em alemão.
Pressione ENTER para interromper a ferramenta.

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source ja-JP` para reconhecer a fala em japonês.
