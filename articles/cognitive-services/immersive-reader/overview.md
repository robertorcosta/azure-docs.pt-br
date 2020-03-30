---
title: O que é a API de Leitura Avançada?
titleSuffix: Azure Cognitive Services
description: A API de Leitura Avançada é uma ferramenta que pode ser usada para acomodar pessoas com diferenças de aprendizado ou ajudar novos leitores e aprendizes de linguagem.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052375"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente desenvolvida que implementa técnicas comprovadas para melhorar a compreensão de leitura para leitores emergentes, aprendizes da idioma e pessoas com diferenças de aprendizado, como dislexia.

Você pode usar a Leitura Avançada no seu aplicativo Web usando o SDK da Leitura Avançada.

## <a name="what-does-immersive-reader-do"></a>O que a Leitura Avançada faz?

A Leitura Avançada foi desenvolvida para tornar a leitura mais acessível a todos.

* Mostra o conteúdo em uma exibição de leitura mínima

  ![Leitura Avançada](./media/immersive-reader.png)

* Exibe imagens de palavras usadas com frequência

  ![Dicionário de Imagens](./media/picture-dictionary.png)

* Realça substantivos, verbos, adjetivos e advérbios

  ![Partes do discurso](./media/parts-of-speech.png)

* Lê o conteúdo em voz alta para você

  ![Leitura em voz alta](./media/read-aloud.png)

* Converte o conteúdo em outro idioma

  ![Tradução](./media/translation.png)

* Divisão de palavras em silabas

  ![Silabação](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como a Leitura Avançada funciona?

A Leitura Avançada é um aplicativo Web autônomo que, quando invocado usando o SDK JavaScript da Leitura Avançada, é exibida sobre o aplicativo Web existente por meio de um `iframe`. Ao chamar a API para iniciar a Leitura Avançada, você pode especificar o conteúdo que deseja mostrar na Leitura Avançada. Nosso SDK trata da criação e do estilo do `iframe`, bem como da comunicação com o serviço de back-end da Leitura Avançada, que processa o conteúdo de partes da fala, a conversão de texto em fala, a tradução, etc.

## <a name="next-steps"></a>Próximas etapas

Introdução à Leitura Avançada:

* Pular para o [início rápido](./quickstart.md)
* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Ler a [Referência do SDK da Leitura Avançada](./reference.md)
