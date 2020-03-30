---
title: Multiclasse uma-vs-todas
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo One-vs-All Multiclass no Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um conjunto de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456073"
---
# <a name="one-vs-all-multiclass"></a>Multiclasse uma-vs-todas

Este artigo descreve como usar o módulo One-vs-All Multiclass no azure Machine Learning designer (visualização). O objetivo é criar um modelo de classificação que possa prever várias classes, usando a abordagem *um contra todos.*

Esse módulo é útil para criar modelos que preveem três ou mais resultados possíveis, quando o resultado depende de variáveis de previsão contínuas ou categóricas. Esse método também permite usar métodos de classificação binária para problemas que exigem várias classes de saída.

### <a name="more-about-one-versus-all-models"></a>Mais sobre modelos um contra todos

Alguns algoritmos de classificação permitem o uso de mais de duas classes por projeto. Outros restringem os resultados possíveis a um dos dois valores (um modelo binário, ou de duas classes). Mas mesmo algoritmos de classificação binária podem ser adaptados para tarefas de classificação de várias classes através de uma variedade de estratégias. 

Este módulo implementa o método one-versus-all, no qual um modelo binário é criado para cada uma das classes de saída múltiplas. O módulo avalia cada um desses modelos binários para as classes individuais contra seu complemento (todas as outras classes do modelo) como se fosse uma questão de classificação binária. Em seguida, o módulo executa a previsão executando esses classificadores binários e escolhendo a previsão com a maior pontuação de confiança.  

Em essência, o módulo cria um conjunto de modelos individuais e, em seguida, mescla os resultados, para criar um único modelo que prevê todas as classes. Qualquer classificador binário pode ser usado como base para um modelo um contra todos.  

Por exemplo, digamos que você configure um modelo [de máquina vetorial de suporte de duas classes](two-class-support-vector-machine.md) e forneça isso como entrada para o módulo Multiclasse One-vs-All. O módulo criaria modelos de máquina vetorial de suporte de duas classes para todos os membros da classe de saída. Em seguida, aplicaria o método um contra todos para combinar os resultados para todas as classes.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Como configurar o classificador Multiclasse One-vs-All  

Este módulo cria um conjunto de modelos de classificação binária para analisar várias classes. Para usar este módulo, você precisa configurar e treinar um modelo *de classificação binária* primeiro. 

Você conecta o modelo binário ao módulo Multiclasse One-vs-All. Em seguida, você treina o conjunto de modelos usando [O Modelo de Trem](train-model.md) com um conjunto de dados de treinamento rotulado.

Quando você combina os modelos, one-vs-All Multiclass cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, mescla os modelos. O módulo faz essas tarefas mesmo que o conjunto de dados de treinamento possa ter vários valores de classe.

1. Adicione o módulo Multiclasse One-vs-All ao seu pipeline no designer. Você pode encontrar este módulo em **Machine Learning - Initialize**, na categoria **Classificação.**

   O classificador Multiclasse One-vs-All não tem parâmetros configuráveis próprios. Quaisquer personalizações devem ser feitas no modelo de classificação binária fornecido como entrada.

2. Adicione um modelo de classificação binária ao pipeline e configure esse modelo. Por exemplo, você pode usar [a máquina vetorial de suporte de duas classes](two-class-support-vector-machine.md) ou a árvore de decisão aumentada de duas [classes](two-class-boosted-decision-tree.md).

3. Adicione o módulo [Modelo de Trem](train-model.md) ao seu pipeline. Conecte o classificador não treinado que é a saída de Multiclasse One-vs-All.

4. Na outra entrada do [Train Model,](train-model.md)conecte um conjunto de dados de treinamento rotulado que tenha vários valores de classe.

5. Envie o oleoduto.

## <a name="results"></a>Resultados

Depois que o treinamento estiver completo, você pode usar o modelo para fazer previsões multiclasse.

Alternativamente, você pode passar o classificador não treinado para [cross-validate Model](cross-validate-model.md) para validação cruzada contra um conjunto de dados de validação rotulado.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
