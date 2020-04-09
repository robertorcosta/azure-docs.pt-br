---
title: Verifique a instância do contêiner de análise de sentimento
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do contêiner análise de sentimentos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876381"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verifique a instância do contêiner de análise de sentimento

1. Selecione a **guia Visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e digite o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`digite ). A página inicial do contêiner é exibida, o que permite que você saiba que o contêiner está em execução.

    ![Exibir a página inicial do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link Descrição da **API do serviço** para ir à página Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e **selecione Experimente.** Os parâmetros são exibidos, o que inclui este exemplo de entrada:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Substitua a entrada pelo seguinte conteúdo JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Definir **showStats** para `true`.

1. Selecione **Executar** para determinar o sentimento do texto.

    O modelo que é embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta JSON que foi devolvida inclui sentimento para a entrada de texto atualizada:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Agora podemos correlacionar `id` o documento dos dados JSON da carga de `id`resposta ao documento de carga útil de solicitação original . A pontuação de `0.98` mais do que indica um sentimento muito positivo.