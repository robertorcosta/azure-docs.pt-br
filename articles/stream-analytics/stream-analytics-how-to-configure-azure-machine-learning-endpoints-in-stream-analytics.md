---
title: Use pontos de extremidade de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como usar funções definidas pelo usuário de Machine Learning no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426193"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integração do Azure Machine Learning Studio (clássico) no Stream Analytics (Preview)
O Stream Analytics suporta funções definidas pelo usuário que chamam os pontos finais do Azure Machine Learning Studio (clássico). O suporte da API REST para esse recurso é detalhado na [biblioteca de API REST do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações complementares necessárias para a implementação bem-sucedida desse recurso no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Visão geral: Terminologia do Azure Machine Learning Studio (clássico)
O Microsoft Azure Machine Learning Studio (clássico) fornece uma ferramenta colaborativa de arrastar e soltar que você pode usar para construir, testar e implantar soluções de análise preditiva em seus dados. Esta ferramenta é chamada *de Azure Machine Learning Studio (clássico)*. O estúdio é usado para interagir com os recursos do Machine Learning e para compilar, testar e iterar facilmente em seu design. Veja abaixo esses recursos e suas definições.

* **Workspace**: o *workspace* é um contêiner que mantém todos os outros recursos de Machine Learning em um contêiner para gerenciamento e controle.
* **Experimento**: os *Experimentos* são criados por cientistas de dados a fim de usar conjuntos de dados e treinar um modelo de aprendizado de máquina.
* **Endpoint**: *Endpoints* são o objeto Azure Machine Learning Studio (clássico) usado para tomar recursos como entrada, aplicar um modelo de aprendizado de máquina especificado e devolver a saída de pontuação.
* **Serviço da Web de Pontuação**: um *serviço da Web de pontuação* é uma coleção de pontos de extremidade, conforme mencionado acima.

Cada ponto de extremidade tem APIs para execução em lote e execução síncrona. O Stream Analytics usa a execução síncrona. O serviço específico é chamado de [Serviço de Solicitação/Resposta](../machine-learning/studio/consume-web-services.md) no Azure Machine Learning Studio (clássico).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Recursos de Machine Learning necessários para trabalhos do Stream Analytics
Para que o processamento de trabalhos do Stream Analytics seja bem-sucedido, é necessário ter um ponto de extremidade de Solicitação/Resposta, uma [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)e uma definição do Swagger. O Stream Analytics tem um ponto de extremidade adicional que constrói a URL do ponto de extremidade de swagger, procura a interface e retorna uma definição de UDF padrão para o usuário.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar uma UDF de Stream Analytics e Machine Learning por meio da API REST
Com as APIs REST, você pode configurar seu trabalho para chamar funções de Linguagem de Máquina do Azure. As etapas são as seguintes:

1. Criar um trabalho de Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma UDF (função definida pelo usuário)
5. Criar uma transformação do Stream Analytics que chama a UDF
6. Iniciar o trabalho

## <a name="creating-a-udf-with-basic-properties"></a>Criação de uma UDF com propriedades básicas
Como exemplo, o seguinte código de amostra cria um UDF escalar chamado *newudf* que se liga a um ponto final do Azure Machine Learning Studio (clássico). Observe que o *ponto de extremidade* (URI de serviço) pode ser encontrado na página de ajuda da API do serviço escolhido e a *apiKey* pode ser encontrada na página principal de Serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Exemplo de corpo de solicitação:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamar o ponto de extremidade RetrieveDefaultDefinition para UDF padrão
Após a criação do esqueleto da UDF, é necessário obter a definição completa da UDF. O ponto final RetrieveDefaultDefinition ajuda você a obter a definição padrão para uma função escalar que está vinculada a um ponto final do Azure Machine Learning Studio (clássico). A carga abaixo exige que você obtenha definição padrão da UDF para uma função escalar associada a um ponto de extremidade de Azure Machine Learning. Ela não especifica o ponto de extremidade real, pois ele já foi fornecido durante a solicitação PUT. O Stream Analytics chamará o ponto de extremidade fornecido na solicitação se ele for fornecido explicitamente. Caso contrário, ele usa o ponto referenciado originalmente. Neste exemplo, a UDF usa um parâmetro de cadeia de caracteres única (uma sentença) e retorna uma saída única do tipo de cadeia de caracteres que indica o rótulo "sentimento" daquela sentença.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Exemplo de corpo de solicitação:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Um exemplo disso teria uma aparência como esta.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Aplicar patch à UDF com a resposta
Agora a UDF deve ser corrigida com a resposta anterior, conforme exibido abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo da solicitação (saída de RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar uma transformação do Stream Analytics que chama a UDF
Agora, consulte a UDF (chamada aqui de scoreTweet) para cada evento de entrada e grave uma resposta para esse evento em uma saída.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Comece a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
