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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156469"
---
**Metas de computação podem ser reutilizadas de um trabalho de treinamento para o outro**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.  Para pipelines de aprendizado de máquina, use a etapa de [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) apropriada para cada meta de computação.

|Metas &nbsp;de treinamento|[ML automatizado](../articles/machine-learning/concept-automated-ml.md) | [Gasodutos ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Cluster de computação azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sim, & <br/>ajuste hiperparâmetro&nbsp; | sim | sim |
|[VM remoto](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sim, & <br/>gráfico de ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sim (somente no modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
