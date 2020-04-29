---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422358"
---
## <a name="speech-modes"></a>Modos de fala

**Interactive (Interativo)**
- Destinado para cenários de comando e controle.
- Tem um valor de tempo limite de segmentação de X.
- No final de um expressão reconhecido, o serviço para de processar o áudio dessa ID de solicitação e termina a rodada. A conexão não está fechada.
- O limite máximo para o reconhecimento é 20s.
- A chamada carbono típica para Invoke `RecognizeOnceAsync`é.

**Escreve**
- Destinado a reconhecimentos mais longos.
- Tem um valor de tempo limite de segmentação de Y. (Y! = X)
- Processará várias declarações completas sem encerrar a rodada.
- Terminará a opção de muita silêncio.
- O carbono continuará com uma nova ID de solicitação e repetirá o áudio conforme necessário.
- O serviço forçará a desconexão após 10 minutos de reconhecimento de fala.
- O carbono reconectará e repetirá o áudio não confirmado.
- Chamado em carbono com `StartContinuousRecognition`.

**Comandos**
- Permite que os usuários especifiquem a pontuação falando nisso.
- Chamado em carbono especificando `EnableDictation` no `SpeechConfig` objeto, independentemente da chamada à API que inicia o reconhecimento.
- O cluster<sup>st</sup> de parte 1 St `speech.fragment` retorna mensagens para resultados intermediários,<sup>os 3</sup> mensagens `speech.hypothesis` de retorno de terceiros.