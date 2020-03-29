---
title: Verifique a instância do contêiner de detecção de idiomas
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do contêiner de detecção de idiomas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968543"
---
### <a name="verify-the-language-detection-container-instance"></a>Verifique a instância do contêiner de detecção de idiomas

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

1. Definir **showStats** para `true`.

1. Selecione **Executar** para determinar o sentimento do texto.

    O modelo que é embalado no recipiente gera uma pontuação que varia de 0 a 1, onde 0 é sentimento negativo e 1 é sentimento positivo.

    A resposta JSON que foi devolvida inclui sentimento para a entrada de texto atualizada:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Agora podemos correlacionar os documentos dos dados JSON da carga de resposta aos `id`documentos de carga de solicitação originais pelos seus correspondentes . Cada documento é tratado independentemente contendo `characterCount` `transactionCount`várias estatísticas como e . Além disso, cada documento `detectedLanguages` resultante tem `name` `iso6391Name`a `score` matriz com o , e para cada idioma detectado. Quando várias línguas são `score` detectadas, a é usada para determinar a linguagem mais provável.