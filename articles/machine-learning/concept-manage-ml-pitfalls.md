---
title: Gerencie armadilhas comuns do modelo ML com aprendizado automático de máquina.
titleSuffix: Azure Machine Learning
description: Identifique e gerencie armadilhas comuns de modelos ML com as soluções automatizadas de aprendizado de máquina do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/27/2020
ms.openlocfilehash: e0bc1aa48dfb40ea146fa79fdfd57da841ca1404
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385526"
---
# <a name="manage-ml-pitfalls-with-automated-machine-learning"></a>Gerencie armadilhas ml com aprendizado de máquina automatizado

Dados superajustados e desequilibrados são armadilhas comuns quando você constrói modelos de aprendizado de máquina. Por padrão, o aprendizado automatizado de máquina do Azure Machine Learning fornece gráficos e métricas para ajudá-lo a identificar esses riscos e implementa práticas recomendadas para ajudar a atenuá-los. 

## <a name="identify-over-fitting"></a>Identificar o excesso de encaixe

O excesso de encaixe no aprendizado de máquina ocorre quando um modelo se encaixa muito bem nos dados de treinamento e, como resultado, não pode prever com precisão dados de testes invisíveis. Em outras palavras, o modelo simplesmente memorizou padrões específicos e ruídos nos dados de treinamento, mas não é flexível o suficiente para fazer previsões sobre dados reais.

Considere os seguintes modelos treinados e suas respectivas precisãos de trem e teste.

| Modelo | Precisão do trem | Precisão do teste |
|-------|----------------|---------------|
| Um | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A,** há um equívoco comum de que se a precisão do teste em dados invisíveis for menor do que a precisão do treinamento, o modelo é superequipado. No entanto, a precisão do teste deve ser sempre menor do que a precisão do treinamento, e a distinção para excesso de ajuste versus ajuste adequado se resume a *quanto* menos preciso. 

Ao comparar os modelos **A** e **B,** o modelo **A** é um modelo melhor porque tem maior precisão de teste, e embora a precisão do teste seja ligeiramente menor em 95%, não é uma diferença significativa que sugere que o excesso de encaixe está presente. Você não escolheria o modelo **B** simplesmente porque as precisãos do trem e do teste estão mais próximas.

O modelo **C** representa um caso claro de excesso de encaixe; a precisão do treinamento é muito alta, mas a precisão do teste não é tão alta. Essa distinção é subjetiva, mas vem do conhecimento do seu problema e dados, e quais magnitudes de erro são aceitáveis.

## <a name="prevent-over-fitting"></a>Evitar o excesso de encaixe

Nos casos mais flagrantes, um modelo superequipado assumirá que as combinações de valor de recurso vistas durante o treinamento sempre resultarão na mesma saída para o alvo.

A melhor maneira de evitar o excesso de encaixe é seguir as melhores práticas da ML, incluindo:

* Usando mais dados de treinamento e eliminando viés estatístico
* Prevenção de vazamento de alvo
* Usando menos recursos
* **Regularização e otimização de hiperparâmetros**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto do ML automatizado, os três primeiros itens acima são **as práticas recomendadas que você implementa.** Os últimos três itens em negrito são **implementos ML automatizados de práticas recomendadas** por padrão para proteger contra o excesso de encaixe. Em outras configurações além do ML automatizado, todas as seis práticas recomendadas valem a pena seguir para evitar modelos superadequados.

### <a name="best-practices-you-implement"></a>Práticas recomendadas que você implementa

O uso **de mais dados** é a maneira mais simples e possível de evitar o excesso de encaixe, e como um bônus adicional normalmente aumenta a precisão. Quando você usa mais dados, torna-se mais difícil para o modelo memorizar padrões exatos, e é forçado a alcançar soluções mais flexíveis para acomodar mais condições. Também é importante reconhecer **viés estatístico,** para garantir que seus dados de treinamento não incluam padrões isolados que não existirão em dados de previsão ao vivo. Este cenário pode ser difícil de resolver, porque pode não haver excesso de encaixe entre o seu trem e os conjuntos de teste, mas pode haver um presente de ajuste excessivo quando comparado com dados de teste ao vivo.

O vazamento de alvo é um problema semelhante, onde você pode não ver excesso de encaixe entre os conjuntos de trem/teste, mas sim aparece na hora da previsão. O vazamento de destino ocorre quando o modelo "trapaceia" durante o treinamento, tendo acesso a dados que normalmente não deveria ter na hora da previsão. Por exemplo, se o seu problema é prever na segunda-feira o que um preço de commodity será na sexta-feira, mas um de seus recursos acidentalmente incluiu dados de quinta-feira, que seriam dados que o modelo não terá em tempo de previsão, já que não pode ver no futuro. Vazamento de alvo é um erro fácil de perder, mas muitas vezes é caracterizado por precisão anormalmente alta para o seu problema. Se você está tentando prever o preço das ações e treinou um modelo com 95% de precisão, provavelmente há vazamento de alvo em algum lugar em suas características.

A remoção de recursos também pode ajudar com a supermontagem, impedindo que o modelo tenha muitos campos para usar para memorizar padrões específicos, fazendo com que ele seja mais flexível. Pode ser difícil medir quantitativamente, mas se você pode remover características e manter a mesma precisão, você provavelmente tornou o modelo mais flexível e reduziu o risco de super-ajuste.

### <a name="best-practices-automated-ml-implements"></a>Implementos ML automatizados de práticas recomendadas

A regularização é o processo de minimizar uma função de custo para penalizar modelos complexos e superequipados. Existem diferentes tipos de funções de regularização, mas, em geral, todas penalizam o tamanho, variância e complexidade do coeficiente do modelo. O ML automatizado usa L1 (Lasso), L2 (Ridge) e ElasticNet (L1 e L2 simultaneamente) em diferentes combinações com diferentes configurações de hiperparâmetro de modelo que controlam a sobremontagem. Em termos simples, o ML automatizado irá variar o quanto um modelo é regulado e escolher o melhor resultado.

O ML automatizado também implementa limitações explícitas de complexidade do modelo para evitar o excesso de encaixe. Na maioria dos casos, essa implementação é especificamente para algoritmos de árvore de decisão ou floresta, onde a profundidade máxima de árvores individuais é limitada, e o número total de árvores usadas em técnicas florestais ou de conjuntos são limitados.

A validação cruzada (CV) é o processo de pegar muitos subconjuntos de seus dados completos de treinamento e treinar um modelo em cada subconjunto. A ideia é que um modelo possa ter "sorte" e ter grande precisão com um subconjunto, mas usando muitos subconjuntos o modelo não alcançará essa alta precisão todas as vezes. Ao fazer cv, você fornece um conjunto de dados de rebloqueio de validação, especifica suas dobras cv (número de subconjuntos) e o ML automatizado treinará seu modelo e ajustará hiperparâmetros para minimizar o erro no seu conjunto de validação. Uma dobra cv pode ser excessiva, mas usando muitos deles reduz a probabilidade de que seu modelo final esteja em excesso. A compensação é que o CV resulta em tempos de treinamento mais longos e, portanto, maior custo, pois em vez de treinar um modelo uma vez, você o treina uma vez para cada *subconjunto n* CV. 

> [!NOTE]
> A validação cruzada não está habilitada por padrão; ele deve ser configurado em configurações automatizadas de ML. No entanto, depois que a validação cruzada é configurada e um conjunto de dados de validação foi fornecido, o processo é automatizado para você. Consulte 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desequilibrados

Dados desequilibrados são comumente encontrados em dados para cenários de classificação de aprendizagem de máquina, e se refere a dados que contêm uma proporção desproporcional de observações em cada classe. Esse desequilíbrio pode levar a um efeito positivo falsamente percebido da precisão de um modelo, pois os dados de entrada têm viés em relação a uma classe, o que resulta no modelo treinado para imitar esse viés. 

Como os algoritmos de classificação são comumente avaliados pela precisão, verificar a pontuação de precisão de um modelo é uma boa maneira de identificar se ele foi impactado por dados desequilibrados. Tinha precisão muito alta ou precisão muito baixa para certas classes?

Além disso, as corridas automatizadas de ML geram os seguintes gráficos automaticamente, o que pode ajudá-lo a entender a correção das classificações do seu modelo e identificar modelos potencialmente impactados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de Confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia os rótulos corretamente classificados em relação aos rótulos reais dos dados. 
[Recall de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a razão dos rótulos corretos em relação à razão das instâncias de rótulos encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a razão dos rótulos corretos em relação à razão de rótulos falso-positivos.

## <a name="handle-imbalanced-data"></a>Lidar com dados desequilibrados 

Como parte de seu objetivo de simplificar o fluxo de trabalho de aprendizado de máquina, o ML automatizado construiu recursos para ajudar a lidar com dados desequilibrados, como, 

- Uma **coluna de peso**: ML automatizado suporta uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas para cima ou para baixo, o que pode tornar uma classe mais ou menos "importante".

- Os algoritmos usados pelo ML automatizado podem lidar adequadamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

As seguintes técnicas são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- Reamostragem até mesmo o desequilíbrio de classe, seja pela amostragem para cima das classes menores ou pela amostragem para baixo das classes maiores. Esses métodos exigem expertise para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação da F1 é uma média ponderada de precisão e recall. A precisão mede a exatidão de um classificador- baixa precisão indica um alto número de falsos positivos--, enquanto o recall mede a completude de um classificador - baixa recordação indica um alto número de falsos negativos. 

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e aprenda a construir modelos usando aprendizado automático de máquina:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com o Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as configurações para experimento de treinamento automático:
  + No estúdio Azure Machine Learning, [use essas etapas](how-to-use-automated-ml-for-ml-models.md).
  + Com o Python SDK, [use essas etapas](how-to-configure-auto-train.md).


