---
title: 'Modelo de pontuação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Score Model no Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364180"
---
# <a name="score-model-module"></a>Módulo de Modelo de Pontuação

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como usar

1. Adicione o módulo **Score Model** ao seu pipeline.

2. Anexar um modelo treinado e um conjunto de dados contendo novos dados de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado que você está usando. O esquema do conjunto de dados de entrada também deve coincidir com o esquema dos dados utilizados para treinar o modelo.

3. Envie o oleoduto.

## <a name="results"></a>Resultados

Depois de ter gerado um conjunto de pontuações usando [o Score Model](./score-model.md):

+ Para gerar um conjunto de métricas usadas para avaliar a precisão (desempenho) do modelo, você pode conectar o conjunto de dados pontuados ao [Modelo de Avaliação,](./evaluate-model.md) 
+ Clique com o botão direito do mouse no módulo e selecione **Visualizar** para ver uma amostra dos resultados.
<!-- + To Save the results to a dataset. -->

A pontuação, ou valor previsto, pode ser em muitos formatos diferentes, dependendo do modelo e seus dados de entrada:

- Para modelos de classificação, [o Score Model](./score-model.md) produz um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de [regressão, o Score Model](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço web

Um uso comum de pontuação é retornar a saída como parte de um serviço web preditivo. Para obter mais informações, consulte [este tutorial](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) sobre como implantar um ponto final em tempo real baseado em um pipeline no designer de Machine Learning do Azure.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 