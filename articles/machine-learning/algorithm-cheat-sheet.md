---
title: Folha de trapaça do algoritmo de aprendizagem de máquina
titleSuffix: Azure Machine Learning
description: Uma folha de trapaça de algoritmo de aprendizagem de máquina imprimível ajuda você a escolher o algoritmo certo para o seu modelo preditivo no designer de Machine Learning do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328601"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Planilha de trapaça do algoritmo de aprendizagem de máquina para o designer de aprendizado de máquina do Azure

A **Folha de Referências de Algoritmo do Azure Machine Learning** ajuda a escolher o algoritmo certo para o modelo de análise preditiva.

O Azure Machine Learning possui uma grande biblioteca de algoritmos das famílias de ***classificação,*** ***sistemas de recomendação,*** ***agrupamento,*** ***detecção de anomalias,*** ***regressão***e ***análise de texto.*** Cada um foi projetado para atender a um tipo diferente de problema de aprendizado de máquina.

Para obter orientação adicional, [consulte Como selecionar algoritmos](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Planilha de trapaça do algoritmo de aprendizagem de máquina

**Baixe a folha de consulta daqui: [Folha de consulta de algoritmos do Machine Learning (11 x 17 pol.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Folha de trapaça do algoritmo de aprendizagem de máquina: aprenda a escolher um algoritmo de Aprendizado de Máquina.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Baixar e imprimir a folha de consulta do algoritmo do Machine Learning em tamanho tabloide para mantê-lo à mão e obter ajuda para escolher um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a folha de trapaça do algoritmo de aprendizagem de máquina

As sugestões oferecidas nessa página de dicas úteis de algoritmo são aproximadas às regras de bolso. Algumas podem ser ajustadas e algumas podem ser flagrantemente violadas. Esta folha de trapaça tem a intenção de sugerir um ponto de partida. Não tenha medo de comparar vários algoritmos em seus dados. Simplesmente não há substituto para entender os princípios de cada algoritmo e o sistema que gerou seus dados.

Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou tendência indutiva. Para um problema específico, vários algoritmos podem ser apropriados, e um algoritmo pode ser um ajuste melhor do que outros. Mas nem sempre é possível saber com antecedência qual é a melhor opção. Em casos como esse, vários algoritmos estão listados juntos na folha de consulta. Uma estratégia apropriada seria um algoritmo e se os resultados ainda não estiverem satisfatórios, tentar os outros. 

Para saber mais sobre os algoritmos no Azure Machine Learning, acesse o Algoritmo e a [referência do módulo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizado de máquina

Há três categorias principais de aprendizado de máquina: *aprendizado supervisionado*, *aprendizado sem supervisão* e *aprendizado de reforço*.

### <a name="supervised-learning"></a>Aprendizado supervisionado

Em aprendizado supervisionado, cada ponto de dados é rotulado ou associado a uma categoria ou um valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como, por exemplo, um gato ou um cão. Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo do aprendizado supervisionado é estudar vários exemplos rotulados como esses e, em seguida, conseguir fazer previsões sobre os pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de vendas precisos a outros carros usados. Este é um tipo popular e útil de aprendizado de máquina.

### <a name="unsupervised-learning"></a>Aprendizado não supervisionado

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. Grupos de aprendizagem não supervisionados em clusters, como o K-means faz, ou encontra diferentes maneiras de olhar para dados complexos para que pareçam mais simples.

### <a name="reinforcement-learning"></a>Aprendizado de reforço

No aprendizado de reforço, o algoritmo escolhe uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa. Com base nesse sinal, o algoritmo modifica sua estratégia para alcançar a maior recompensa. 

## <a name="next-steps"></a>Próximas etapas

* Veja orientações adicionais sobre [como selecionar algoritmos](how-to-select-algorithms.md)

* [Conheça o estúdio no Azure Machine Learning e o portal Azure](overview-what-is-azure-ml.md).

* [Tutorial: Construa um modelo de previsão no designer de Machine Learning do Azure.](tutorial-designer-automobile-price-train-score.md)

* [Aprenda sobre aprendizagem profunda versus aprendizado de máquina.](concept-deep-learning-vs-machine-learning.md)
