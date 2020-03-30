---
title: 'Importância do recurso de permutação: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Permutation Feature Importance no Azure Machine Learning para calcular as pontuações de importância das variáveis de recurso, dado um modelo treinado e um conjunto de dados de teste.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456056"
---
# <a name="permutation-feature-importance"></a>Importância do recurso de permuta

Este artigo descreve como usar o módulo Permutation Feature Importance no Azure Machine Learning designer (preview), para calcular um conjunto de pontuações de importância de recursos para o seu conjunto de dados. Você usa essas pontuações para ajudá-lo a determinar os melhores recursos para usar em um modelo.

Neste módulo, os valores de recurso são embaralhados aleatoriamente, uma coluna de cada vez. O desempenho do modelo é medido antes e depois. Você pode escolher uma das métricas padrão para medir o desempenho.

As pontuações que o módulo retorna representam a *mudança* no desempenho de um modelo treinado, após a permutação. Características importantes geralmente são mais sensíveis ao processo de embaralhar, por isso resultarão em pontuações de maior importância. 

Este artigo fornece uma visão geral do recurso de permutação, sua base teórica e suas aplicações em aprendizado de máquina: [Permutation Feature Importance](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Como usar a importância do recurso de permutação

Gerar um conjunto de pontuações de recursos requer que você tenha um modelo já treinado, bem como um conjunto de dados de teste.  

1.  Adicione o módulo 'Importância do recurso de permutação' ao seu pipeline. Você pode encontrar este módulo na categoria **Seleção de Recursos.** 

2.  Conecte um modelo treinado à entrada esquerda. O modelo deve ser um modelo de regressão ou um modelo de classificação.  

3.  Na entrada certa, conecte um conjunto de dados. De preferência, escolha um que seja diferente do conjunto de dados que você usou para treinar o modelo. Este conjunto de dados é usado para pontuação com base no modelo treinado. Também é usado para avaliar o modelo depois que os valores de recurso mudaram.  

4.  Para **sementes aleatórias,** insira um valor para usar como semente para randomização. Se você especificar 0 (o padrão), um número será gerado com base no relógio do sistema.

     Um valor de semente é opcional, mas você deve fornecer um valor se quiser reprodutibilidade em todas as corridas do mesmo pipeline.  

5.  Para **Métrica para medir o desempenho,** selecione uma única métrica a ser usada quando estiver computando a qualidade do modelo após a permutação.  

     O designer de Machine Learning do Azure suporta as seguintes métricas, dependendo se você está avaliando um modelo de classificação ou regressão:  

    -   **Classificação**

        Precisão, precisão, recall  

    -   **Regressão**

        Precisão, Recall, Erro Absoluto Médio, Erro Quadrado da Média Raiz, Erro Absoluto Relativo, Erro Relativo Quadrado, Coeficiente de Determinação  

     Para obter uma descrição mais detalhada dessas métricas de avaliação e como elas são calculadas, consulte [Avaliar modelo](evaluate-model.md).  

6.  Envie o oleoduto.  

7.  O módulo produz uma lista de colunas de recursos e as pontuações associadas a elas. A lista está classificada em ordem decrescente das pontuações.  


##  <a name="technical-notes"></a>Observações técnicas

Recurso de permutação A importância funciona alterando aleatoriamente os valores de cada coluna de recurso, uma coluna de cada vez. Em seguida, avalia o modelo. 

Os rankings que o módulo fornece são muitas vezes diferentes dos que você recebe da [Seleção de Recursos Baseados em Filtros](filter-based-feature-selection.md). A seleção de recursos baseados em filtro calcula pontuações *antes de* um modelo ser criado. 

A razão para a diferença é que a importância do recurso de permutação não mede a associação entre um recurso e um valor-alvo. Em vez disso, captura quanta influência cada recurso tem nas previsões do modelo.
  
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
