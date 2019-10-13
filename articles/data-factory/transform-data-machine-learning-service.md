---
title: Executar Azure Machine Learning pipelines de serviço em seus pipelines de Azure Data Factory | Microsoft Docs
description: Saiba como executar seus pipelines de serviço do Azure Machine Learning em seus pipelines de Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301341"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>Executar Azure Machine Learning pipelines de serviço no Azure Data Factory

Execute seus pipelines de serviço Azure Machine Learning como uma etapa em seus pipelines de Azure Data Factory. A atividade Machine Learning executar pipeline permite cenários de previsão de lote, como a identificação de possíveis padrões de empréstimo, a determinação de sentimentos e a análise de padrões de comportamento do cliente.

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade no pipeline | Cadeia de caracteres | Sim
type | O tipo de atividade é ' AzureMLExecutePipeline ' | Cadeia de caracteres | Sim
linkedServiceName | Serviço vinculado ao serviço de Azure Machine Learning | Referência de serviço vinculado | Sim
mlPipelineId | ID do pipeline de Azure Machine Learning publicado | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim
experimentoname | Nome de teste de execução do histórico da execução do pipeline de Machine Learning | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
mlPipelineParameters | Pares de chave, valor a serem passados para o ponto de extremidade do pipeline de Azure Machine Learning publicado. As chaves devem corresponder aos nomes dos parâmetros de pipeline definidos no pipeline de Machine Learning publicado | Objeto com pares chave-valor (ou expressão com o objeto ResultType) | Não
mlParentRunId | A ID de execução do pipeline do serviço Azure Machine Learning pai | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
continueOnStepFailure | Se deseja continuar a execução de outras etapas no pipeline de Machine Learning executar se uma etapa falhar | boolean | Não

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
