---
title: 'Modelo de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de pontuação em Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80364180"
---
# <a name="score-model-module"></a>Módulo de Modelo de Pontuação

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como usar

1. Adicione o módulo **modelo de Pontuação** ao seu pipeline.

2. Anexe um modelo treinado e um conjunto de dados que contenha novos dado de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado que você está usando. O esquema do conjunto de dados de entrada também deve corresponder ao esquema dos dados usados para treinar o modelo.

3. Envie o pipeline.

## <a name="results"></a>Resultados

Depois de gerar um conjunto de pontuações usando o [modelo de Pontuação](./score-model.md):

+ Para gerar um conjunto de métricas usadas para avaliar a precisão do modelo (desempenho), você pode conectar o conjunto de pontos de Pontuação para [avaliar o modelo](./evaluate-model.md), 
+ Clique com o botão direito do mouse no módulo e selecione **Visualizar** para ver um exemplo dos resultados.
<!-- + To Save the results to a dataset. -->

A pontuação, ou o valor previsto, pode estar em vários formatos diferentes, dependendo do modelo e dos dados de entrada:

- Para modelos de classificação, o [modelo de Pontuação](./score-model.md) gera um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de regressão, o [modelo de Pontuação](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço Web

Um uso comum de pontuação é retornar a saída como parte de um serviço Web de previsão. Para obter mais informações, consulte [este tutorial](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) sobre como implantar um ponto de extremidade em tempo real com base em um pipeline no designer de Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 