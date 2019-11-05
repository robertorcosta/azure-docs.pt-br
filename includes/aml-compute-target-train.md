---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489587"
---
**Os destinos de computação podem ser reutilizados de um trabalho de treinamento para o próximo**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.

|Treinamento &nbsp;destinos| Suporte de GPU |[ML automatizado](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Designer de Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Eu | Sim | &nbsp; | &nbsp; |
|[Azure Machine Learning instância de computação](../articles/machine-learning/service/concept-compute-instance.md)| Sim | | Sim |  |
|[Azure Machine Learning cluster de computação](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Sim | Sim & <br/>ajuste de&nbsp;de hiperparâmetro | Sim | Sim |
|[VM remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Sim | Sim & <br/>gráfico de ajuste de hiperparâmetro | Sim | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Sim | Sim | &nbsp; |
|[Análise Azure Data Lake](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Sim | &nbsp; |
