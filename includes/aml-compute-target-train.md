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
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703381"
---
**Os destinos de computação podem ser reutilizados pelos trabalhos de treinamento**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.  Para pipelines de machine learning, use a [etapa do pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) apropriada para cada destino de computação.

|Destinos de &nbsp;treinamento|[ML automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Cluster de computação do Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| sim e <br/>ajuste de&nbsp;hiperparâmetro | sim | sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | sim e <br/>gráfico de ajuste de hiperparâmetro | sim |  |
|[VM remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | sim e <br/>gráfico de ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sim (somente no modo local do SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
