---
title: 'Início Rápido: Detectar anomalias nos dados de série temporal usando o Java e a API REST do Detector de Anomalias'
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API do Detector de Anomalias para detectar anormalidades na sua série de dados como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 42edf9cd1687314f2ef168969791cb39f43851d3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323682"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Início Rápido: Detectar anomalias nos dados de série temporal usando o Java e a API REST do Detector de Anomalias

Use este início rápido para começar a usar os dois modos de detecção da API do Detector de Anomalias para detectar anomalias nos dados da série temporal. Este aplicativo Java envia duas solicitações de API contendo dados de série temporal formatados em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON contendo o status de anomalias (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON contendo o status de anomalias (e outros dados) para o ponto de dados mais recente nos dados da série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. Você pode encontrar o código-fonte deste Início Rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
- Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Crie um recurso de Detector de Anomalias"  target="_blank">Crie um recurso de detector de anomalias <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    - Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do Detector de Anomalias. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
- O JDK ([Java&trade; Development Kit) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.
- Importar essas bibliotecas do repositório do Maven
    - Pacote [JSON em Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Um arquivo JSON contendo pontos de dados de série temporal. Os dados de exemplo deste Início Rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Crie um projeto Java e importe as bibliotecas a seguir.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Crie variáveis para seu ponto de extremidade e sua chave de assinatura. Abaixo estão os URIs que você pode usar para a detecção de anomalias. Eles serão anexados a seu ponto de extremidade de serviço posteriormente para criar as URLs de solicitação da API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção em lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função chamada `sendRequest()` que use as variáveis criadas anteriormente. Em seguida, siga estas etapas.

2. Crie um objeto `CloseableHttpClient` que pode enviar solicitações à API. Envie a solicitação para um objeto de solicitação `HttpPost` combinando o ponto de extremidade e a URL do Detector de Anomalias.

3. Use a função `setHeader()` da solicitação para definir o cabeçalho `Content-Type` como `application/json` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

4. Use a função `setEntity()` da solicitação para os dados a serem enviados.

5. Use a função `execute()` do cliente para enviar a solicitação e salve-a em um objeto `CloseableHttpResponse`.

6. Crie um objeto `HttpEntity` para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie um método chamado `detectAnomaliesBatch()` para detectar anomalias nos dados como um lote. Chame o método `sendRequest()` criado acima com seu ponto de extremidade, sua URL, sua chave de assinatura e seus dados JSON. Obtenha o resultado e imprima-o no console.

2. Se a resposta contiver o campo `code`, imprima o código de erro e a mensagem de erro.

3. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém um valor booliano relacionado a se um determinado ponto de dados é uma anomalia. Obtenha a matriz JSON e itere por ela, imprimindo o índice de qualquer valor `true`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

Crie um método chamado `detectAnomaliesLatest()` para detectar o status da anomalia do último ponto de dados no conjunto de dados. Chame o método `sendRequest()` criado acima com seu ponto de extremidade, sua URL, sua chave de assinatura e seus dados JSON. Obtenha o resultado e imprima-o no console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar seus dados da série temporal e enviar a solicitação

1. No método principal do aplicativo, leia o arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame as duas funções de detecção de anomalias criadas acima.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção em lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta de detecção do ponto mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
