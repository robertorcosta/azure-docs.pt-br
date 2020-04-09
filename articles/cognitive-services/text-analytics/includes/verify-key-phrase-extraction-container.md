---
title: Verifique a instância do contêiner de extração de frases-chave
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do contêiner Deextração de frases-chave.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876382"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Verifique a instância do contêiner de extração de frases-chave

1. Selecione a **guia Visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e digite o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`digite ). A página inicial do contêiner é exibida, o que permite que você saiba que o contêiner está em execução.

    ![Exibir a página inicial do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link Descrição da **API do serviço** para ir à página Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e **selecione Experimente.** Os parâmetros são exibidos, o que inclui este exemplo de entrada:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    O modelo que está embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é negativo e 1 é positivo.

    A resposta JSON que foi devolvida inclui sentimento para a entrada de texto atualizada:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Agora podemos correlacionar `id` o documento dos dados JSON da carga de `id`resposta ao documento de carga útil de solicitação original . O documento resultante `keyPhrases` possui uma matriz, que contém a lista de frases-chave que foram extraídas do documento de entrada correspondente. Além disso, existem várias estatísticas como `characterCount` e `transactionCount` para cada documento resultante.