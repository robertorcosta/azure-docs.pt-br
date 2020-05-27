---
title: Avaliar e mitigar a imparcialidade em modelos de machine learning
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a imparcialidade em modelos de machine learning e como o pacote Fairlearn para Python pode ajudá-lo a criar modelos mais razoáveis.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596355"
---
# <a name="fairness-in-machine-learning-models"></a>Imparcialidade nos modelos de machine learning

Saiba mais sobre a imparcialidade em machine learning e como o pacote de software livre Fairlearn para Python pode ajudá-lo a criar modelos mais razoáveis.

## <a name="what-is-fairness-in-machine-learning-systems"></a>O que é imparcialidade nos sistemas de machine learning?

Os sistemas de inteligência artificial e de machine learning podem exibir um comportamento injusto. Uma maneira de definir o comportamento injusto é por seus danos ou pelo impacto sobre as pessoas. Há muitos tipos de danos que os sistemas de IA causar. Dois tipos comuns de danos causados pela IA são:

- Danos de alocação: Um sistema de IA estende ou retém oportunidades, recursos ou informações. Os exemplos incluem contratações, admissões em escolas e empréstimos em que um modelo pode ser muito melhor em escolher bons candidatos entre um grupo específico de pessoas do que entre outros grupos.

- Danos de qualidade de serviço: Um sistema de IA não funciona tão bem para um grupo de pessoas como funciona para outro. Por exemplo, um sistema de reconhecimento de voz pode não funcionar tão bem para mulheres como funciona para homens.

Para reduzir o comportamento injusto em sistemas de IA, você precisa avaliar e atenuar esses danos.

>[!NOTE]
> A imparcialidade é um desafio sociotécnico. Muitos aspectos de imparcialidade, como justiça e processo devido, não são capturados em métricas de imparcialidade quantitativas. Além disso, muitas métricas de imparcialidade quantitativas não podem ser atendidas simultaneamente. O objetivo é permitir que as pessoas avaliem estratégias de mitigação diferentes e, em seguida, façam compensações apropriadas para o seu cenário.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Avaliação e mitigação de imparcialidade com Fairlearn

O Fairlearn é um pacote de software livre para Python que permite que os desenvolvedores de sistemas de machine learning avaliem a imparcialidade dos seus sistemas e atenuem os problemas de imparcialidade observados.

O Fairlearn tem dois componentes:

- Painel de avaliação: Um widget do Jupyter Notebook para avaliar como as previsões de um modelo afetam diferentes grupos. Ele também permite a comparação de vários modelos usando métricas de desempenho e imparcialidade.
- Algoritmos de mitigação: Um conjunto de algoritmos para atenuar a parcialidade na classificação e regressão binárias.

Juntos, esses componentes permitem que os cientistas de dados e os líderes de negócios naveguem por quaisquer compensações entre a imparcialidade e o desempenho e selecionem a estratégia de mitigação que melhor atenda às suas necessidades.

## <a name="fairness-assessment"></a>Avaliação de imparcialidade

No Fairlearn, a imparcialidade é conceituada através de uma abordagem conhecida como **imparcialidade de grupo**, que pergunta: Quais grupos de indivíduos estão em risco de sofrer danos?

Os grupos relevantes, também conhecidos como subpopulações, são definidos por meio de **recursos sensíveis** ou atributos sensíveis. Os recursos sensíveis são passados para um avaliador do Fairlearn como um vetor ou uma matriz com o nome de `sensitive_features`. O termo sugere que o designer do sistema deve ser sensível a esses recursos ao avaliar a imparcialidade de grupo. Algo com o qual se deve ter atenção é se esses recursos contêm implicações de privacidade devido a informações de identificação pessoal. Mas a palavra “sensível” não significa que esses recursos não devem ser usados para fazer previsões.

Durante a fase de avaliação, a imparcialidade é quantificada por meio de métricas de disparidade. As **métricas de disparidade** podem avaliar e comparar o comportamento do modelo entre diferentes grupos como proporções ou como diferenças. O Fairlearn dá suporte a duas classes de métricas de disparidade:


- Disparidade no desempenho do modelo: Esses conjuntos de métricas calculam a disparidade (diferença) nos valores da métrica de desempenho selecionada em diferentes subgrupos. Alguns exemplos incluem:

  - disparidade na taxa de precisão
  - disparidade na taxa de erro
  - disparidade na precisão
  - disparidade no recall
  - disparidade de MAE
  - muitos outros

- Disparidade na taxa de seleção: Essa métrica contém a diferença na taxa de seleção entre subgrupos diferentes. Um exemplo disso é a disparidade na taxa de aprovação de empréstimo. Taxa de seleção significa a fração de pontos de dados em cada classe classificada como 1 (em classificação binária) ou distribuição de valores de previsão (em regressão).

## <a name="unfairness-mitigation"></a>Mitigação de parcialidade

### <a name="parity-constraints"></a>Restrições de paridade

O Fairlearn inclui diversos algoritmos de mitigação de parcialidade. Esses algoritmos dão suporte a um conjunto de restrições no comportamento do previsor chamado **restrições de paridade** ou critérios. As restrições de paridade exigem que alguns aspectos do comportamento do previsor sejam comparáveis em todos os grupos que os recursos sensíveis definem (por exemplo, corridas diferentes). Os algoritmos de mitigação do Fairlearn usam restrições de paridade para mitigar os problemas de parcialidade observados.

O Fairlearn dá suporte aos seguintes tipos de restrições de paridade:

|Restrição de paridade  | Finalidade  |Ferramenta de machine learning  |
|---------|---------|---------|
|Paridade demográfica     |  Mitigar danos de alocação | Classificação binária, regressão |
|Chances equalizadas  | Diagnosticar a alocação e os danos de qualidade de serviço | Classificação binária        |
|Perda de grupo limitada     |  Mitigar os danos de qualidade de serviço | Regressão |

### <a name="mitigation-algorithms"></a>Algoritmos de mitigação

O Fairlearn fornece algoritmos de pós-processamento e redução de mitigação de parcialidade:

- Reduction: Esses algoritmos pegam um avaliador de ML caixa preta padrão (por exemplo, um modelo de LightGBM) e geram um conjunto de modelos readaptados usando uma sequência de conjuntos de valores de treinamento reponderados. Por exemplo, é possível aumentar o diminuir o peso de candidatos de um determinado gênero para readaptar os modelos e reduzir as disparidades em diferentes grupos de gêneros. Os usuários podem, então, escolher um modelo que forneça a melhor compensação entre precisão (ou outra métrica de desempenho) e disparidade, que geralmente precisaria ser baseada em regras de negócio e cálculos de custo.  
- Pós-processamento: Esses algoritmos usam um classificador existente e o recurso sensível como entrada. Em seguida, eles produzem uma transformação da previsão do classificador para impor as restrições de imparcialidade especificadas. A maior vantagem da otimização do limite é sua simplicidade e flexibilidade, pois não é necessário readaptar o modelo. 

| Algoritmo | Descrição | Ferramenta de machine learning | Recursos sensíveis | Restrições de paridade suportadas | Tipo de algoritmo: |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Abordagem de caixa preta para classificação justa descrita em [Uma abordagem de reduções para classificação justa](https://arxiv.org/abs/1803.02453) | Classificação binária | Categóricos | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Redução |
| `GridSearch` | Abordagem de caixa preta descrita em [Uma abordagem de reduções para classificação justa](https://arxiv.org/abs/1803.02453)| Classificação binária | Binário | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Redução |
| `GridSearch` | Abordagem de caixa preta que implementa uma variante de pesquisa de grade da regressão justa com o algoritmo para perda de grupo limitada descrito em [Regressão justa: Definições quantitativas e algoritmos baseados em redução](https://arxiv.org/abs/1905.12843) | Regressão | Binário | [Perda de grupo limitada](#parity-constraints) | Redução |
| `ThresholdOptimizer` | Algoritmo de pré-processamento baseado no documento [Igualdade de oportunidades em aprendizado supervisionado](https://arxiv.org/abs/1610.02413). Essa técnica usa como entrada um classificador existente e o recurso sensível, e produz uma transformação monotônica da previsão do classificador para impor as restrições de paridade especificadas. | Classificação binária | Categóricos | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Pós-processamento |

## <a name="next-steps"></a>Próximas etapas

- Para saber como usar os diferentes componentes, confira o [repositório GitHub do Fairlearn](https://github.com/fairlearn/fairlearn/) e os [exemplos de notebook](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Aprenda como preservar a privacidade dos dados usando a [Privacidade diferencial e o pacote WhisperNoise](concept-differential-privacy.md).