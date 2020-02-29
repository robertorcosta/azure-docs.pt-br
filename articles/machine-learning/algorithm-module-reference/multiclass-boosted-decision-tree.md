---
title: 'Árvore de decisão aumentada multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo árvore de decisão aumentada multiclasse no Azure Machine Learning para criar um classificador usando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920052"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de Decisão Aumentada Multiclasse

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada.

Uma árvore de decisão aumentada é um método de aprendizado Ensemble no qual a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige os erros da primeira e segunda árvores e assim por diante. As previsões são baseadas na Ensemble de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizado supervisionado, você precisa de um conjunto de informações *rotulado* que inclua uma coluna de rótulo com um valor para todas as linhas.

Você pode treinar esse tipo de modelo usando o [modelo de treinamento](././train-model.md). 

1.  Adicione o módulo **árvore de decisão aumentada multiclasse** ao seu pipeline.

1.  Especifique como você deseja que o modelo seja treinado definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.  

1. O **número máximo de folhas por árvore** limita o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. O **número mínimo de amostras por nó folha** indica o número de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. A **taxa de aprendizagem** define o tamanho da etapa durante o aprendizado. Insira um número entre 0 e 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Número de árvores construídas** indica o número total de árvores de decisão a serem criadas no Ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.

1. A **semente de número aleatório** define opcionalmente um inteiro não negativo para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](./train-model.md) .

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
