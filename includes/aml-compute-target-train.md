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
ms.openlocfilehash: 9a04bca11c6fca480ea1c9fd3d4a51956c1830de
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933096"
---
**Os destinos de computação podem ser reutilizados pelos trabalhos de treinamento**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.  Para pipelines de machine learning, use a [etapa do pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) apropriada para cada destino de computação.

|Destinos de &nbsp;treinamento|[ML automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Cluster de computação do Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| sim e <br/>ajuste de&nbsp;hiperparâmetro | sim | sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | sim e <br/>gráfico de ajuste de hiperparâmetro | sim |  |
|[VM remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | sim e <br/>gráfico de ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-set-up-training-targets.md#databricks)| sim (somente no modo local do SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-set-up-training-targets.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
