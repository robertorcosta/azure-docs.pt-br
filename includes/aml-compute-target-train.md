---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77156469"
---
**Os destinos de computação podem ser reutilizados de um trabalho de treinamento para o próximo**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.  Para pipelines de Machine Learning, use a [etapa de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) apropriada para cada destino de computação.

|Metas &nbsp;de treinamento|[ML automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de Machine Learning](../articles/machine-learning/concept-ml-pipelines.md) | [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster de computação](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sim & <br/>ajuste de&nbsp;hiperparâmetro | sim | sim |
|[VM remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sim & <br/>gráfico de ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Sim (somente no modo local do SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
