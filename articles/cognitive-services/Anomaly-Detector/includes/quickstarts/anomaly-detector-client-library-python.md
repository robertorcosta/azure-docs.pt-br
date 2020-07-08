---
title: Início rápido da biblioteca de cliente Python do Detector de Anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 2fd4faf9fa553f8fb7b6553c0d1435bf003b5578
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986086"
---
Introdução à biblioteca de clientes do Detector de Anomalias para Python. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço Detector de Anomalias permite localizar anormalidades em seus dados de série temporal usando automaticamente os modelos de melhor ajuste, independentemente do setor, cenário ou volume de dados.

Use a biblioteca de cliente do Detector de Anomalias para Python para:

* Detectar anomalias em todo o seu conjunto de dados de série temporal, como uma solicitação em lote
* Detectar o status de anomalias do ponto de dados mais recente em sua série temporal

[Documentação de referência da biblioteca](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Localize o código de exemplo no GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)
* A [biblioteca de análise de dados do Pandas](https://pandas.pydata.org/)
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Crie um recurso de Detector de Anomalias"  target="_blank">Crie um recurso de detector de anomalias <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do Detector de Anomalias. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

 
## <a name="setting-up"></a>Configurando

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

 Crie um arquivo do Python e importe as bibliotecas a seguir.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Crie variáveis para sua chave como uma variável de ambiente, o caminho para um arquivo de dados de série temporal e a localização do Azure de sua assinatura. Por exemplo, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Detector de Anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) que se autentica no Azure usando sua chave. O cliente fornece dois métodos de detecção de anomalias: Em um conjunto de dados inteiro usando [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)e no ponto de dados mais recente usando [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Os dados de série temporal são enviados como uma série de [Pontos](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) em um objeto [Solicitação](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). O objeto `Request` contém propriedades para descrever os dados ([Granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python), por exemplo) e os parâmetros para a detecção de anomalias. 

A resposta do Detector de Anomalias é um objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) ou [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python), dependendo do método usado. 

## <a name="code-examples"></a>Exemplos de código 

Esses snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do Detector de Anomalias para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de série temporal de um arquivo](#load-time-series-data-from-a-file)
* [Detectar anomalias no conjunto de dados inteiro](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalias do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione sua variável de localização do Azure ao ponto de extremidade e autentique o cliente com a sua chave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Carregar dados de série temporal de um arquivo

Baixe os dados de exemplo deste guia de início rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Em seu navegador, clique com o botão direito do mouse em **Raw**.
2. Clique em **Salvar link como**.
3. Salve o arquivo no diretório do aplicativo como um arquivo .csv.

Esses dados de série temporal são formatados como um arquivo .csv e enviados à API do Detector de Anomalias.

Carregue seu arquivo de dados com o método `read_csv()` da biblioteca do Pandas e crie uma variável de lista vazia para armazenar sua série de dados. Itere pelo arquivo e acrescente os dados como um objeto [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Esse objeto conterá o carimbo de data/hora e o valor numérico das linhas de seu arquivo de dados .csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Crie um objeto [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) com sua série temporal e a [granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou periodicidade) de seus pontos de dados. Por exemplo, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalias no conjunto de dados inteiro 

Chame a API para detectar anomalias em todos os dados da série temporal usando o método [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) do cliente. Armazene o objeto [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) retornado. Itere pela lista de `is_anomaly` da resposta e imprima o índice de qualquer valor `true`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

Chame a API do Detector de Anomalias para determinar se o seu ponto de dados mais recente é uma anomalia usando o método [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) do cliente e armazene o objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) retornado. O valor `is_anomaly` da resposta é um valor booliano que especifica o status de anomalias daquele ponto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` e o nome do arquivo.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
