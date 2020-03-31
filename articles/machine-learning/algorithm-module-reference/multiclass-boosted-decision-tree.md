---
title: 'Árvore de decisão impulsionada por várias classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Árvore de Decisão Impulsionada multiclasse no Azure Machine Learning para criar um classificador usando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920052"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de Decisão Aumentada Multiclasse

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de aprendizado de máquina que é baseado no algoritmo de árvores de decisão impulsionado.

Uma árvore de decisão impulsionada é um método de aprendizagem de conjunto no qual a segunda árvore corrige para os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante. As previsões são baseadas no conjunto de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizagem supervisionado, você precisa de um *conjunto de dados rotulado* que inclua uma coluna de rótulos com um valor para todas as linhas.

Você pode treinar este tipo de modelo usando o [Modelo de Trem](././train-model.md). 

1.  Adicione o módulo **Árvore de Decisão Aumentada multiclasse** ao seu pipeline.

1.  Especifique como deseja que o modelo seja treinado definindo a opção **Criar modo treinador.**

    + **Parâmetro Único**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.  

1. **O número máximo de folhas por árvore** limita o número máximo de nódulos terminais (folhas) que podem ser criados em qualquer árvore.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **O número mínimo de amostras por nó de folha** indica o número de casos necessários para criar qualquer nó terminal (folha) em uma árvore.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **A taxa de aprendizagem** define o tamanho da etapa durante o aprendizado. Digite um número entre 0 e 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **O número de árvores construídas** indica o número total de árvores de decisão a serem criadas no conjunto. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.

1. **A semente de números aleatórios** define opcionalmente um inteiro não negativo para usar como o valor aleatório das sementes. Especificar uma semente garante a reprodutibilidade em corridas que tenham os mesmos dados e parâmetros.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Se você definir **Criar modo de treinador** para Um **Parâmetro,** conecte um conjunto de dados marcado e o módulo [Modelo de Trem.](./train-model.md)

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
