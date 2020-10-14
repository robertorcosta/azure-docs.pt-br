---
title: 'Início Rápido: Detectar anomalias como lote usando a API REST do Detector de Anomalias e o Python'
titleSuffix: Azure Cognitive Services
description: Use a API do Detector de Anomalias para detectar anormalidades em sua série de dados como um lote ou em dados de streaming neste início rápido.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 0fd82cf6c63e3cae811885e0cbe48e4b3dae0f12
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019810"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Início Rápido: Detectar anomalias nos dados de série temporal usando o Python e a API REST do Detector de Anomalias

Use este início rápido para começar a usar os dois modos de detecção da API do Detector de Anomalias para detectar anomalias nos dados da série temporal. Este aplicativo Python envia solicitações de API contendo dados de série temporal formatados em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON contendo o status de anomalias (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON contendo o status de anomalias (e outros dados) para o ponto de dados mais recente nos dados da série temporal.|
| Detectar pontos de alteração que marcam novas tendências de dados | A resposta JSON que contém os pontos de alteração detectados nos dados de série temporal. |

 Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. Você pode encontrar o código-fonte deste Início Rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
- Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Crie um recurso de Detector de Anomalias"  target="_blank">Crie um recurso de detector de anomalias <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    - Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do Detector de Anomalias. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
- [Python 2.x ou 3.x](https://www.python.org/downloads/)
- A [Biblioteca de solicitações](https://pypi.org/project/requests/) do Python

- Um arquivo JSON contendo pontos de dados de série temporal. Os dados de exemplo deste Início Rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Crie um arquivo do Python e adicione as importações a seguir.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Crie variáveis para seu ponto de extremidade e sua chave de assinatura. Abaixo estão os URIs que você pode usar para a detecção de anomalias. Eles serão anexados a seu ponto de extremidade de serviço posteriormente para criar as URLs de solicitação da API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção em lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Detecção de ponto de alteração | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Leia no arquivo de dados JSON abrindo-o e usando `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função chamada `send_request()` que use as variáveis criadas anteriormente. Em seguida, siga estas etapas.

2. Crie um dicionário para os cabeçalhos de solicitação. Defina `Content-Type` como `application/json` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

3. Envie a solicitação usando `requests.post()`. Combine a URL de detecção de anomalias e de ponto de extremidade para a URL de solicitação completa e inclua os cabeçalhos e os dados da solicitação json. Em seguida, retorne a resposta.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie um método chamado `detect_batch()` para detectar anomalias nos dados como um lote. Chame o método `send_request()` criado acima com o ponto de extremidade, a URL, a chave de assinatura e os dados JSON que pertencem a você.

2. Chame `json.dumps()` no resultado para formatá-lo e imprimi-lo no console.

3. Se a resposta contiver o campo `code`, imprima o código de erro e a mensagem de erro.

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém um valor booliano relacionado a se um determinado ponto de dados é uma anomalia. Itere pela lista e imprima o índice de qualquer valor `True`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

1. Crie um método chamado `detect_latest()` para determinar se o ponto de dados mais recente da série de tempo é uma anomalia. Chame o método `send_request()` acima com o seu ponto de extremidade, a URL, a chave de assinatura e os dados JSON que pertencem a você.

2. Chame `json.dumps()` no resultado para formatá-lo e imprimi-lo no console.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="detect-change-points-in-the-data"></a>Detectar pontos de alteração nos dados

1. Crie um método chamado `detect_change_point()` para detectar anomalias nos dados como um lote. Chame o método `send_request()` criado acima com o ponto de extremidade, a URL, a chave de assinatura e os dados JSON que pertencem a você.

2. Chame `json.dumps()` no resultado para formatá-lo e imprimi-lo no console.

3. Se a resposta contiver o campo `code`, imprima o código de erro e a mensagem de erro.

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo `isChangePoint` da resposta contém um valor booliano que indica se um determinado ponto de dados é uma anomalia. Itere pela lista e imprima o índice de qualquer valor `True`. Esses valores correspondem aos índices dos pontos de alteração de tendência, caso algum tenha sido encontrado.

    [!code-python[detect change points](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectChangePoint)]

## <a name="send-the-request"></a>Enviar a solicitação

Chame os métodos de detecção de anomalias criados acima.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção em lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta de detecção do ponto mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Exemplo de resposta de detecção do ponto de alteração](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
