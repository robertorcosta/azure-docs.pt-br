---
title: Executar pipelines de aprendizado de máquina do Azure
description: Aprenda a executar seus pipelines de Machine Learning do Azure em seus pipelines da Fábrica de Dados Do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: b54504cf8ca7b32bf14bd4b7e0c561ffd56d4098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155156"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Execute os pipelines de aprendizado de máquina do Azure na fábrica de dados do Azure

Execute seus pipelines de Aprendizado de Máquina do Azure como um passo em seus pipelines da Fábrica de Dados Do Azure. A atividade do Machine Learning Execute Pipeline permite cenários de previsão de lotes, como identificar possíveis inadimplências de empréstimos, determinar sentimentos e analisar padrões de comportamento do cliente.

O vídeo abaixo apresenta uma introdução de seis minutos e uma demonstração deste recurso.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

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

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade no pipeline | String | Sim
type | Tipo de atividade é 'AzureMLExecutePipeline' | String | Sim
linkedServiceName | Serviço vinculado ao Aprendizado de Máquina do Azure | Referência de serviço vinculado | Sim
mlPipelineId | ID do pipeline publicado azure Machine Learning | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim
experimentName | Executar o nome do experimento de história do pipeline de Aprendizado de Máquina executado | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
mlPipelineParâmetros | Chave, pares de valor a serem passados para o ponto final do pipeline azure Machine Learning publicado. As chaves devem corresponder aos nomes dos parâmetros de pipeline definidos no pipeline de Machine Learning publicado | Objeto com pares de valores-chave (ou Expressão com objeto resultType) | Não
mlParentRunId | O pipeline de aprendizado de máquina azure pai executar ID | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
continueOnStepFailure | Se continuar a execução de outras etapas no pipeline de Machine Learning executado se um passo falhar | booleano | Não

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Atividade suína](transform-data-using-hadoop-pig.md)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade do procedimento armazenado](transform-data-using-stored-procedure.md)
