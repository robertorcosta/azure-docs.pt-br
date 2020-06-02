---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806452"
---
## <a name="enable-microphone"></a>Habilitar o microfone

Conecte e ligue o microfone do seu PC e desative todos os aplicativos que também possam usar o microfone. Alguns computadores têm um microfone interno, enquanto outros exigem a configuração de um dispositivo Bluetooth.

## <a name="run-the-speech-cli"></a>Executar a CLI de Fala

Agora você está pronto para executar a CLI de Fala para reconhecer a fala do microfone.

1. **Inicie seu aplicativo** – Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e digite:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
    > Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

2. **Inicie o reconhecimento** – Fale no microfone. Você verá a transcrição de suas palavras em texto em tempo real. A CLI de Fala será interrompida após um período de silêncio ou quando você pressionar CTRL-C.
