---
title: Evite superajuste & dados desbalanceado com AutoML
titleSuffix: Azure Machine Learning
description: Identifique e gerencie armadilhas comuns de modelos de ML com as soluções de aprendizado de máquina automatizadas do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 76f920ad6aae68defb567a7a6623d1ffd488af5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80874850"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Evitar sobreajuste e dados desequilibrados com o Machine Learning automatizado

Os dados de ajuste e desequilíbrio são armadilhas comuns quando você cria modelos de aprendizado de máquina. Por padrão, o Machine Learning automatizado do Azure Machine Learning fornece gráficos e métricas para ajudá-lo a identificar esses riscos e implementa práticas recomendadas para ajudar a atenuá-los. 

## <a name="identify-over-fitting"></a>Identificar sobreajuste

O ajuste excessivo no aprendizado de máquina ocorre quando um modelo se ajusta também aos dados de treinamento e, como resultado, não pode prever com precisão os dados de teste não vistos. Em outras palavras, o modelo tem apenas padrões específicos memorizados e ruídos nos dados de treinamento, mas não é flexível o suficiente para fazer previsões sobre dados reais.

Considere os seguintes modelos treinados e suas imprecisões de treinamento e teste correspondentes.

| Modelo | Precisão do treinamento | Precisão do teste |
|-------|----------------|---------------|
| Um | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A**, há um equívoco comum de que, se a precisão do teste nos dados não vistos for menor do que a precisão do treinamento, o modelo será superajustado. No entanto, a precisão do teste deve ser sempre menor do que a precisão do treinamento, e a distinção de sobreajuste vs. adequado se ajusta ao *quanto* menos preciso. 

Ao comparar os modelos **a** e **B**, o modelo **A** é um modelo melhor porque tem precisão de teste mais alta e, embora a precisão do teste seja ligeiramente menor do que 95%, não é uma diferença significativa que sugere que o excesso de ajuste esteja presente. Você não escolheria o modelo **B** simplesmente porque as imprecisões de treinamento e de teste estão mais próximas.

O modelo **C** representa um caso claro de sobreajuste; a precisão do treinamento é muito alta, mas a precisão do teste não é quase tão alta. Essa distinção é subjetiva, mas vem do conhecimento de seu problema e dados e de quais magnitudes de erro são aceitáveis.

## <a name="prevent-over-fitting"></a>Evitar sobreajuste

Na maioria dos casos flagrantes, um modelo de sobreadaptação assumirá que as combinações de valor de recurso vistas durante o treinamento resultarão sempre na mesma saída para o destino.

A melhor maneira de evitar o ajuste excessivo é seguir as práticas recomendadas do ML, incluindo:

* Usando mais dados de treinamento e eliminando a tendência estatística
* Evitando o vazamento de destino
* Usando menos recursos
* **Regularização e otimização de hiperparâmetro**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto de ML automatizado, os três primeiros itens acima são **as práticas recomendadas que você implementa**. Os últimos três itens em negrito são **as melhores práticas que o ml automatizado implementa** por padrão para proteger contra sobreajuste. Em configurações diferentes de ML automatizado, vale a pena seguir todas as seis práticas recomendadas para evitar modelos de sobreajuste.

### <a name="best-practices-you-implement"></a>Práticas recomendadas que você implementa

Usar **mais dados** é a maneira mais simples e melhor possível de evitar o ajuste excessivo, e como um bônus adicional normalmente aumenta a precisão. Quando você usa mais dados, fica mais difícil para o modelo memorizar padrões exatos e é forçado a alcançar soluções que são mais flexíveis para acomodar mais condições. Também é importante reconhecer a **diferença estatística**, para garantir que os dados de treinamento não incluam padrões isolados que não existam em dados de previsão dinâmica. Esse cenário pode ser difícil de resolver, pois pode não haver sobreajuste entre seus conjuntos de treinamento e teste, mas pode haver sobreajustes presentes em comparação com os dados de teste ao vivo.

O vazamento de destino é um problema semelhante, no qual você pode não ver o ajuste excessivo entre os conjuntos de treinamento/teste, mas, em vez disso, ele aparece em tempo de previsão. O vazamento de destino ocorre quando seu modelo "trapacear" durante o treinamento, tendo acesso aos dados que normalmente não deveria ter no tempo de previsão. Por exemplo, se o problema for prever na segunda-feira, qual será o preço de mercadoria na sexta-feira, mas um de seus recursos incluía dados acidentalmente das quintas, que seriam dados que o modelo não terá no tempo de previsão, pois não poderá vê-lo no futuro. O vazamento de destino é um erro fácil de ser ignorado, mas geralmente é caracterizado por uma precisão muito alta para o seu problema. Se você estiver tentando prever o preço de ações e tiver treinado um modelo com precisão de 95%, é provável que haja vazamento de destino em algum lugar em seus recursos.

A remoção de recursos também pode ajudar com o sobreajuste, impedindo que o modelo tenha muitos campos a serem usados para memorizar padrões específicos, tornando-o mais flexível. Pode ser difícil medir de forma quantitativa, mas se você puder remover recursos e manter a mesma precisão, você provavelmente tornou o modelo mais flexível e reduziu o risco de sobreajuste.

### <a name="best-practices-automated-ml-implements"></a>O ML automatizado de práticas recomendadas implementa

A regularização é o processo de minimizar uma função de custo para penalizar modelos complexos e sobreajustados. Há diferentes tipos de funções de regularização, mas, em geral, todos eles penalizam o tamanho do coeficiente do modelo, a variância e a complexidade. O ML automatizado usa L1 (laço), L2 (saliência) e ElasticNet (L1 e L2 simultaneamente) em combinações diferentes com configurações de hiperparâmetro de modelo diferentes que controlam o sobreajuste. Em termos simples, o ML automatizado vai variar o quanto um modelo é regulamentado e escolher o melhor resultado.

O ML automatizado também implementa limitações de complexidade de modelo explícitas para evitar o ajuste excessivo. Na maioria dos casos, essa implementação é especificamente para algoritmos de árvore de decisão ou de floresta, em que a profundidade máxima da árvore individual é limitada e o número total de árvores usadas nas técnicas de floresta ou Ensemble são limitados.

A CV (validação cruzada) é o processo de pegar muitos subconjuntos de seus dados de treinamento completos e treinar um modelo em cada subconjunto. A ideia é que um modelo poderia ter "sorte" e ter grande precisão com um subconjunto, mas usando muitos subconjuntos o modelo não atingirá essa alta precisão a cada vez. Ao fazer a CV, você fornece um conjunto de um DataSet de validação, especifica as dobras de CV (número de subconjuntos) e o ML automatizado treinará seu modelo e ajustará os hiperparâmetros para minimizar o erro em seu conjunto de validação. Uma dobra de CV pode ser mais adequada, mas usar muitas delas reduz a probabilidade de que o modelo final seja mais adequado. A compensação é que a vc resulta em tempos de treinamento mais longos e, portanto, um custo maior, porque, em vez de treinar um modelo uma vez, você o treina uma vez para cada um dos *n* conjuntos de vc. 

> [!NOTE]
> A validação cruzada não está habilitada por padrão; Ele deve ser configurado em configurações de ML automatizadas. No entanto, depois que a validação cruzada estiver configurada e um conjunto de dados de validação tiver sido fornecido, o processo será automatizado para você. Consulte 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desbalanceado

Dados desequilibrados geralmente são encontrados em dados para cenários de classificação de aprendizado de máquina e referem-se a dados que contêm uma proporção desproporcional de observações em cada classe. Esse desequilíbrio pode levar a um efeito positivo falso e perceptível da precisão de um modelo, pois os dados de entrada têm a tendência de uma classe, o que resulta no modelo treinado para imitar essa tendência. 

Como os algoritmos de classificação geralmente são avaliados por exatidão, a verificação da Pontuação de precisão de um modelo é uma boa maneira de identificar se ele foi afetado por dados desequilibrados. Tem realmente alta precisão ou precisão muito baixa para determinadas classes?

Além disso, as execuções automáticas de ML geram automaticamente os gráficos a seguir, o que pode ajudá-lo a entender a exatidão das classificações do modelo e identificar os modelos potencialmente afetados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia os rótulos classificados corretamente em relação aos rótulos reais dos dados. 
[Recall de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a proporção de rótulos corretos em relação à taxa de instâncias de rótulo encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a proporção de rótulos corretos em relação à taxa de rótulos falsos positivos.

## <a name="handle-imbalanced-data"></a>Lidar com dados desbalanceado 

Como parte de seu objetivo de simplificar o fluxo de trabalho do Machine Learning, o ML automatizado tem recursos internos para ajudar a lidar com dados desequilibrados, como 

- Uma **coluna de peso**: o ml automatizado dá suporte a uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas ou reduzidas, o que pode tornar uma classe mais ou menos "importante".

- Os algoritmos usados pelo ML automatizado podem lidar corretamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

As técnicas a seguir são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- A reamostragem até mesmo do desequilíbrio de classe, seja por amostragem das classes menores ou da amostragem das classes maiores. Esses métodos exigem experiência para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação F1 é uma média ponderada de precisão e RECALL. A precisão mede a exatidão de um classificador – a precisão baixa indica um alto número de falsos positivos--,, enquanto a RECALL mede a integridade de um classificador, a RECALL baixa indica um grande número de falsos negativos. 

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e aprenda a criar modelos usando o aprendizado de máquina automatizado:

+ Siga o [tutorial: treinar automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md)

+ Defina as configurações para o teste de treinamento automático:
  + No Azure Machine Learning Studio, [Use estas etapas](how-to-use-automated-ml-for-ml-models.md).
  + Com o SDK do Python, [Use estas etapas](how-to-configure-auto-train.md).


