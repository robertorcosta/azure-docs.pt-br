---
title: 'Árvore de decisão impulsionada por duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Árvore de Decisão Impulsionada de Duas Classes no Azure Machine Learning para criar um modelo de aprendizado de máquina que se baseia no algoritmo de árvores de decisão impulsionado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920732"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisão impulsionada de duas classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de aprendizado de máquina que é baseado no algoritmo de árvores de decisão impulsionado. 

Uma árvore de decisão impulsionada é um método de aprendizagem de conjunto no qual a segunda árvore corrige para os erros da primeira árvore, a terceira árvore corrige para os erros da primeira e segunda árvores, e assim por diante.  As previsões são baseadas em todo o conjunto de árvores juntos que faz a previsão.
  
Geralmente, quando configuradas corretamente, árvores de decisão impulsionadas são os métodos mais fáceis para obter o melhor desempenho em uma ampla variedade de tarefas de aprendizado de máquina. No entanto, eles também são um dos alunos mais intensivos em memória, e a implementação atual mantém tudo na memória. Portanto, um modelo de árvore de decisão impulsionado pode não ser capaz de processar os grandes conjuntos de dados que alguns alunos lineares podem lidar.

## <a name="how-to-configure"></a>Como configurar

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizagem supervisionado, para treinar o modelo, você precisa de um *conjunto de dados marcado* que inclua uma coluna de rótulos com um valor para todas as linhas.

Você pode treinar este tipo de modelo usando [o Modelo de Trem](././train-model.md). 

1.  No Azure Machine Learning, adicione o módulo **Árvore de Decisão Impulsionada** ao seu pipeline.
  
2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**
  
    + **Parâmetro Único**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.
  
    + **Intervalo de parâmetros**: Se você não tiver certeza dos melhores parâmetros, você pode encontrar os parâmetros ideais usando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Você fornece algumas faixas de valores, e o treinador itera sobre várias combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para **O número máximo de folhas por árvore,** indique o número máximo de nódulos terminais (folhas) que podem ser criados em qualquer árvore.
  
     Aumentando este valor, você aumenta potencialmente o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.
  
4.  Para **Número mínimo de amostras por nó folha,** indique o número de casos necessários para criar qualquer nó terminal (folha) em uma árvore.  
  
     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.
  
5.  Para **taxa de aprendizagem,** digite um número entre 0 e 1 que define o tamanho da etapa durante o aprendizado.  
  
     A taxa de aprendizagem determina o quão rápido ou lento o aluno converge na solução ideal. Se o tamanho da etapa for muito grande, você pode ultrapassar a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento leva mais tempo para convergir na melhor solução.
  
6.  Para **o número de árvores construídas,** indique o número total de árvores de decisão a serem criadas no conjunto. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.
  
     Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se você quiser ver ou imprimir uma única árvore, defina o valor como 1. No entanto, quando você faz isso, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas outras iterações.
  
7.  Para **sementes de número aleatório, digite**opcionalmente um inteiro não negativo para usar como valor de semente aleatório. Especificar uma semente garante a reprodutibilidade em corridas que tenham os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por padrão como 0, o que significa que o valor inicial da semente é obtido no relógio do sistema.  Sucessivas corridas usando uma semente aleatória podem ter resultados diferentes.
  

9. Treinar o modelo.
  
    + Se você definir **Criar modo de treinador** para Um **Parâmetro,** conecte um conjunto de dados marcado e o módulo [Modelo de Trem.](./train-model.md)  
   
## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo para pontuação, adicione o módulo **Score Model** a um pipeline.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 