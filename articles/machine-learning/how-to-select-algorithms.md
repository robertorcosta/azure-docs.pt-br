---
title: Como selecionar um algoritmo de aprendizagem de máquina
titleSuffix: Azure Machine Learning
description: Como selecionar algoritmos de Machine Learning do Azure para aprendizado supervisionado e não supervisionado em experimentos de clustering, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328656"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Como selecionar algoritmos para Aprendizado de Máquina do Azure

Uma pergunta comum é :"Qual algoritmo de aprendizagem de máquina devo usar?" O algoritmo selecionado depende principalmente de dois aspectos diferentes do seu cenário de ciência de dados:

 - **O que você quer fazer com seus dados?** Especificamente, qual é a pergunta de negócios que você quer responder aprendendo com seus dados passados?

 - **Quais são os requisitos do seu cenário de ciência de dados?** Especificamente, qual é a precisão, tempo de treinamento, linearidade, número de parâmetros e número de recursos que sua solução suporta?

 ![Considerações para escolher algoritmos: O que você quer saber? Quais são os requisitos do cenário?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Cenários de negócios e a folha de trapaça do algoritmo de aprendizagem de máquina

A [Planilha de Trapaça do Algoritmo de Aprendizagem de Máquina do Azure](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) ajuda você com a primeira consideração: O que você quer fazer com seus **dados**? Na Planilha cheat do algoritmo de aprendizagem de máquina, procure tarefas que você deseja fazer e, em seguida, encontre um algoritmo [de designer de Machine Learning do Azure](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) para a solução de análise preditiva. 

O designer de Machine Learning fornece um portfólio abrangente de algoritmos, como [Floresta de Decisão Multiclasse,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) [Sistemas de Recomendação,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri) [Regressão de Rede Neural,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) [Rede Neural Multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)e [Agrupamento de Meios K.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) Cada algoritmo foi projetado para resolver um tipo diferente de problema de aprendizagem de máquina. Consulte o [algoritmo de designer de Machine Learning e referência de módulo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) para uma lista completa, juntamente com a documentação sobre como cada algoritmo funciona e como ajustar parâmetros para otimizar o algoritmo.

> [!NOTE]
> Para baixar a folha de trapaça do algoritmo de aprendizagem de máquina, vá para [a folha de trapaça do algoritmo de aprendizagem de máquina Azure](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Juntamente com a orientação na Planilha de Trapaça do Algoritmo de Aprendizagem de Máquina do Azure, tenha em mente outros requisitos ao escolher um algoritmo de aprendizagem de máquina para sua solução. A seguir estão fatores adicionais a serem considerados, como a precisão, o tempo de treinamento, a linearidade, o número de parâmetros e o número de características.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Requisitos adicionais para um cenário de ciência de dados

Uma vez que você sabe o que quer fazer com seus dados, você precisa determinar requisitos adicionais para sua solução. 

Faça escolhas e possivelmente trocas para os seguintes requisitos:

- Precisão
- Tempo de treinamento
- Linearidade
- Número de parâmetros
- Número de recursos

## <a name="accuracy"></a>Precisão

A precisão no aprendizado de máquina mede a eficácia de um modelo como proporção de resultados verdadeiros ao total de casos. No designer de Machine Learning, o [módulo Avaliar modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) calcula um conjunto de métricas de avaliação padrão do setor. Você pode usar este módulo para medir a precisão de um modelo treinado.

Obter a resposta mais precisa possível nem sempre é necessário. Às vezes uma aproximação será adequada, dependendo do uso que você quiser dar a ela. Se esse for o caso, você pode ser capaz de reduzir seu tempo de processamento dramaticamente, aderindo a métodos mais aproximados. Métodos aproximados também tendem naturalmente a evitar ajustes excessivos.

Existem três maneiras de usar o módulo Modelo de Avaliação:

- Gerar pontuações sobre seus dados de treinamento para avaliar o modelo
- Gerar pontuações no modelo, mas comparar essas pontuações com pontuações em um conjunto de testes reservado
- Compare pontuações para dois modelos diferentes, mas relacionados, usando o mesmo conjunto de dados

Para obter uma lista completa de métricas e abordagens que você pode usar para avaliar a precisão dos modelos de aprendizado de máquina, consulte [Avaliar o módulo Modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Tempo de treinamento

No aprendizado supervisionado, treinamento significa usar dados históricos para construir um modelo de aprendizado de máquina que minimize erros. O número de minutos ou de horas necessários para treinar um modelo varia muito entre algoritmos. O tempo de treinamento é frequentemente intimamente ligado à precisão; um normalmente acompanha o outro. 

Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros. Você pode escolher um algoritmo específico porque você tem uma limitação de tempo, especialmente quando o conjunto de dados é grande.

No designer de Machine Learning, criar e usar um modelo de aprendizado de máquina é tipicamente um processo de três etapas:

1.  Configure um modelo, escolhendo um determinado tipo de algoritmo e, em seguida, definindo seus parâmetros ou hiperparâmetros. 

2.  Forneça um conjunto de dados que seja rotulado e tenha dados compatíveis com o algoritmo. Conecte os dados e o modelo ao [módulo Train Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Após a conclusão do treinamento, use o modelo treinado com um dos [módulos](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) de pontuação para fazer previsões sobre novos dados.

## <a name="linearity"></a>Linearidade

Linearidade em estatística e aprendizado de máquina significa que há uma relação linear entre uma variável e uma constante em seu conjunto de dados. Por exemplo, algoritmos de classificação linear assumem que as classes podem ser separadas por uma linha reta (ou seu analógico de maior dimensão).

Muitos algoritmos de aprendizado de máquina usam a linearidade. No designer de Machine Learning do Azure, eles incluem: 

- [Regressão logística multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regressão logística de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Máquinas vetoriais de suporte](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Os algoritmos de regressão linear supõem que as tendências de dados seguem uma linha reta. Essa suposição não é ruim para alguns problemas, mas para outros reduz a precisão. Apesar de suas desvantagens, algoritmos lineares são populares como uma primeira estratégia. Eles tendem a ser algoritmicamente simples e rápidos de treinar.

![Limite de classe não linear](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite de classe não linear***: Confiar em um algoritmo de *classificação linear resultaria em baixa precisão.*

![Dados com uma tendência não linear](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dados com uma tendência não linear***: O uso de um método de *regressão linear geraria erros muito maiores do que o necessário.*

## <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que o cientista de dados precisa girar ao configurar um algoritmo. São números que afetam o comportamento do algoritmo, como tolerância a erros ou número de iterações, ou opções entre variantes de como o algoritmo se comporta. O tempo de treinamento e a precisão do algoritmo às vezes podem ser sensíveis para obter apenas as configurações certas. Normalmente, algoritmos com grande número de parâmetros requerem mais tentativa e erro para encontrar uma boa combinação.

Alternativamente, há o [módulo Tune Model Hyperparameters](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) no designer de Machine Learning: O objetivo deste módulo é determinar os hiperparâmetros ideais para um modelo de aprendizado de máquina. O módulo constrói e testa vários modelos usando diferentes combinações de configurações. Ele compara métricas em todos os modelos para obter as combinações de configurações. 

Embora esta seja uma ótima maneira de garantir que você tenha atravessado o espaço dos parâmetros, o tempo necessário para treinar um modelo aumenta exponencialmente com o número de parâmetros. A vantagem é que ter muitos parâmetros geralmente indica que um algoritmo tem mais flexibilidade. Muitas vezes pode alcançar uma precisão muito boa, desde que você possa encontrar a combinação certa de configurações de parâmetros.

## <a name="number-of-features"></a>Número de recursos

No aprendizado de máquina, uma característica é uma variável quantificável do fenômeno que você está tentando analisar. Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação ao número de pontos de dados. Geralmente, isso acontece com dados de genética ou de texto. 

Um grande número de recursos pode derrubar alguns algoritmos de aprendizagem, tornando o tempo de treinamento inviavelmente longo. [As máquinas vetoriais](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) de suporte são particularmente adequadas para cenários com um alto número de recursos. Por essa razão, eles têm sido usados em muitos aplicativos, desde a recuperação de informações até a classificação de texto e imagem. As máquinas vetoriais de suporte podem ser usadas tanto para tarefas de classificação quanto de regressão.

A seleção de recursos refere-se ao processo de aplicação de testes estatísticos a entradas, dada uma saída especificada. O objetivo é determinar quais colunas são mais preditivas da saída. O [módulo de seleção de recursos baseado em filtro](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) no designer de aprendizado de máquina fornece vários algoritmos de seleção de recursos para escolher. O módulo inclui métodos de correlação, como correlação de Pearson e valores qui-quadrados.

Você também pode usar o [módulo Permutation Feature Importance](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) para calcular um conjunto de pontuações de importância de recurso para o seu conjunto de dados. Em seguida, você pode aproveitar essas pontuações para ajudá-lo a determinar os melhores recursos para usar em um modelo.


## <a name="next-steps"></a>Próximas etapas

 - [Saiba mais sobre o designer de Machine Learning do Azure](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Para obter descrições de todos os algoritmos de aprendizagem de máquina disponíveis no designer de Machine Learning do Azure, consulte [algoritmo de designer de Machine Learning e referência de módulo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Para explorar a relação entre deep learning, machine learning e IA, veja [Deep Learning vs. Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
