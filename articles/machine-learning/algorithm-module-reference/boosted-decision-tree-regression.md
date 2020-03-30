---
title: 'Regressão da árvore de decisão impulsionada: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão de árvore de decisão aumentada no Azure Machine Learning para criar um conjunto de árvores de regressão usando boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456719"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão de árvore de decisão impulsionado

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um conjunto de árvores de regressão usando boosting. *Impulsionar* significa que cada árvore depende de árvores anteriores. O algoritmo aprende encaixando o resíduo das árvores que o precederam. Assim, impulsionada em um ensemble de árvore de decisão tende a aumentar a precisão com um pequeno risco de menos cobertura.  
  
Este método de regressão é um método de aprendizagem supervisionado e, portanto, requer um *conjunto de dados rotulado*. A coluna do rótulo deve conter valores numéricos.  

> [!NOTE]
> Use esse módulo somente com conjuntos de dados que usam variáveis numéricas.  

Depois de definir o modelo, treine-o usando o [Modelo de Trem](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão impulsionadas  

O impulsionamento é um dos vários métodos clássicos para criar modelos ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  No Azure Machine Learning, as árvores de decisão impulsionadas usam uma implementação eficiente do algoritmo de impulsionamento de gradiente MART. O aumento de gradiente é uma técnica de aprendizado de máquina para problemas de regressão. Ele cria cada árvore de regressão por etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo na próxima. Portanto, o modelo de previsão é realmente um ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, o impulso constrói uma série de árvores de forma passo-sábio, e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciada arbitrária.  
  
Para obter mais informações, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipédia sobre o aumento do gradiente fornece alguns antecedentes sobre árvores impulsionadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Pesquisa da Microsoft: Do RankNet ao LambdaRank ao LambdaMART: Uma visão geral. Por J.C. O Burges.

O método de impulsionamento de gradiente também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores impulsionadas para tarefas de classificação, consulte [Árvore de Decisão Impulsionada por Duas Classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão impulsionada

1.  Adicione o módulo **Árvore de Decisão Aumentada** ao seu pipeline. Você pode encontrar este módulo em **Machine Learning**, **Initialize**, na categoria **Regressão.** 
  
2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único :** Selecione esta opção se você souber como deseja configurar o modelo e forneça um conjunto específico de valores como argumentos. 
     
    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.    
   
  
3. **Número máximo de folhas por árvore**: Indique o número máximo de nódulos terminais (folhas) que podem ser criados em qualquer árvore.  

    Aumentando este valor, você aumenta potencialmente o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.  

4. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) em uma árvore.

    Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos 5 casos que atendem as mesmas condições.

5. **Taxa de aprendizado**: Digite um número entre 0 e 1 que define o tamanho da etapa durante o aprendizado. A taxa de aprendizagem determina o quão rápido ou lento o aluno converge na solução ideal. Se o tamanho da etapa for muito grande, você pode ultrapassar a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento leva mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: Indicar o número total de árvores de decisão a serem criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treinamento aumenta.

    Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se você quiser ver ou imprimir uma única árvore, você pode definir o valor como 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas outras iterações.

7. **Semente de número aleatório**: Digite um inteiro opcional não negativo para usar como valor de semente aleatório. Especificar uma semente garante a reprodutibilidade em corridas que tenham os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor inicial da semente é obtido no relógio do sistema.
  

9. Adicione um conjunto de dados de treinamento e um dos módulos de treinamento:

    - Se você definir criar a opção **de modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](train-model.md)  
  
    

10. Envie o oleoduto.  
  
## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para usar o modelo para pontuação, conecte-o ao [Score Model](./score-model.md), para prever valores para novos exemplos de entrada.

+ Para salvar um instantâneo do modelo treinado, selecione A guia **Saídas** no painel direito do **modelo treinado** e clique em Registrar ícone **de conjunto de dados.** A cópia do modelo treinado será salva como um módulo na árvore do módulo e não será atualizada em sucessivas corridas do gasoduto.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
