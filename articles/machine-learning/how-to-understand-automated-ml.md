---
title: Avaliar os resultados do experimento do AutoML
titleSuffix: Azure Machine Learning
description: Saiba como exibir e avaliar os gráficos e as métricas de cada execução de teste automatizado do Machine Learning.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: fcbe0fc5049f6e892f80f048a885c75420bc636e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359078"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Avaliar os resultados do experimento do Machine Learning automatizado

Neste artigo, saiba como exibir e avaliar os resultados de seu Machine Learning automatizado, ML automatizado e experimentos. Esses experimentos consistem em várias execuções, em que cada execução cria um modelo. Para ajudá-lo a avaliar cada modelo, o ML automatizado gera automaticamente métricas de desempenho e gráficos específicos para seu tipo de experimento. 

Por exemplo, o ML automatizado fornece gráficos diferentes para modelos de classificação e regressão. 

|classificação|Regressão
|---|---|
|<li> [Matriz de confusão](#confusion-matrix) <li>[Gráfico de recall de precisão](#precision-recall-chart) <li> [ROC (características operacionais do destinatário)](#roc) <li> [Curva de comparação de precisão](#lift-curve)<li> [Curva de ganhos](#gains-curve)<li> [Gráfico de calibragem](#calibration-plot) | <li> [Previsto versus verdadeiro](#pvt) <li> [Histograma de resíduos](#histo)|

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Crie um experimento para a execução automatizada do Machine Learning, seja com o SDK ou no Azure Machine Learning Studio.

    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo de regressão](tutorial-auto-train-models.md)
    * Use o [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-run-results"></a>Exibir resultados da execução

Após a conclusão do experimento automatizado do Machine Learning, um histórico das execuções pode ser encontrado no seu espaço de trabalho do Machine Learning por meio do [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 

Para experimentos do SDK, você pode ver esses mesmos resultados durante uma execução quando usa o `RunDetails` [widget Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py).

As etapas e a animação a seguir mostram como exibir o histórico de execução e as métricas de desempenho e os gráficos de um modelo específico no estúdio.

![Etapas para exibir o histórico de execução e os gráficos e métricas de desempenho do modelo](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Para exibir o histórico de execuções e os gráficos e as métricas de desempenho do modelo no estúdio: 

1. [Entre no estúdio](https://ml.azure.com/) e navegue até seu espaço de trabalho.
1. No painel esquerdo do espaço de trabalho, selecione **execuções**.
1. Na lista de experimentos, selecione aquele que você deseja explorar.
1. Na tabela inferior, selecione a **execução**.
1. Na guia **modelos** , selecione o **nome do algoritmo** para o modelo que você deseja explorar.
1. Na guia **métricas** , selecione quais métricas e gráficos você deseja avaliar para esse modelo. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Métricas de desempenho de classificação

A tabela a seguir resume as métricas de desempenho do modelo que o ML automatizado calcula para cada modelo de classificação gerado para o experimento. 

Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--
AUC_macro| AUC é a área embaixo da Curva característica operacional do receptor. Macro é a média aritmética do AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC é a área embaixo da Curva característica operacional do receptor. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC é a área embaixo da Curva característica operacional do receptor. Ponderado é a média aritmética da Pontuação de cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy é o percentual de rótulos previstos que coincidem exatamente com os rótulos verdadeiros. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Macro é a média aritmética da Pontuação de precisão média de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Ponderado é a média aritmética da Pontuação de precisão média para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Precisão equilibrada é a média aritmética do recolhimento de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Pontuação F1 é a média harmônica de precisão e recuperação. Macro é a média aritmética da Pontuação F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Pontuação F1 é a média harmônica de precisão e recuperação. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Pontuação F1 é a média harmônica de precisão e recuperação. Média ponderada por frequência de classe ou pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Essa é a função de perda usada em regressão logística (multinomial) e extensões de ti, como redes neurais, definidas como a probabilidade negativa de log dos rótulos verdadeiros, dadas as previsões de um classificador probabilística. Para obter um único exemplo com verdadeiro rótulo YT {0,1} e YP de probabilidade estimada que YT = 1, a perda de log é-log P (yt&#124;YP) =-(YT log (YP) + (1-YT) log (1-YP)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|O Recolhimento de macro normalizado é o recolhimento de macro normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isso é obtido por norm_macro_recall: = (recall_score_macro-R)/(1-R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (ou seja, R = 0,5 para classificação binária e R = (1/C) para problemas de classificação de classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro" |
precision_score_macro|Precisão é a porcentagem de elementos previstos de forma positiva que são rotulados corretamente. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precisão é a porcentagem de elementos previstos de forma positiva que são rotulados corretamente. O micro é computado globalmente, contando os verdadeiros positivos totais e os falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precisão é a porcentagem de elementos previstos de forma positiva que são rotulados corretamente. Ponderado é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall é a porcentagem de elementos rotulados corretamente de uma determinada classe. Macro é a média aritmética de recall para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall é a porcentagem de elementos rotulados corretamente de uma determinada classe. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall é a porcentagem de elementos rotulados corretamente de uma determinada classe. Ponderado é a média aritmética de recall para cada classe, ponderada pelo número de instâncias true em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|A precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na classe verdadeira do exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no destino|

### <a name="binary-vs-multiclass-metrics"></a>Métricas binárias versus multiclasse

O ML automatizado não diferencia entre métricas binárias e multiclasse. As mesmas métricas de validação são relatadas se um conjunto de dado tem duas classes ou mais de duas classes. No entanto, algumas métricas são destinadas à classificação multiclasse. Quando aplicado a um DataSet binário, essas métricas não tratarão nenhuma classe como a `true` classe, como você pode esperar. As métricas que são claramente destinadas a multiclasse são sufixadas com `micro` , `macro` ou `weighted` . Os exemplos incluem,,, `average_precision_score` `f1_score` `precision_score` `recall_score` e `AUC` .

Por exemplo, em vez de calcular a RECALL como `tp / (tp + fn)` , a média de recalls de várias classes ( `micro` , `macro` ou `weighted` ) é calculada em ambas as classes de um conjunto de uma classificação binária. Isso é equivalente a calcular a recall para a `true` classe e a `false` classe separadamente e, em seguida, pegar a média dos dois.

## <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão descreve o desempenho de um modelo de classificação. Cada linha exibe as instâncias da classe true ou real no conjunto de dados, e cada coluna representa as instâncias da classe que foi prevista pelo modelo. 

Para cada matriz de confusão, ML automatizado mostra a frequência de cada rótulo previsto (coluna) comparado com o rótulo verdadeiro (linha). Quanto mais escura a cor, maior a contagem nessa parte específica da matriz. 

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?

Uma matriz de confusão compara o valor real do conjunto de dados com os valores previstos que o modelo forneceu. Por isso, os modelos de aprendizado de máquina têm maior precisão se o modelo tiver a maioria de seus valores ao longo da diagonal, o que significa que o modelo preparou o valor correto. Se um modelo tiver desequilíbrio de classe, a matriz de confusão ajudará a detectar um modelo com tendência.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemplo 1: um modelo de classificação com precisão incorreta
![Um modelo de classificação com precisão incorreta](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemplo 2: um modelo de classificação com alta precisão 
![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemplo 3: um modelo de classificação com alta precisão e alta diferença em previsões de modelo
![Um modelo de classificação com alta precisão e alta tendência em previsões de modelo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Gráfico de precisão/recall

A curva de recall de precisão mostra a relação entre precisão e recall de um modelo. A precisão do termo representa a capacidade de um modelo rotular todas as instâncias corretamente. O termo “recall” representa a capacidade de um classificador localizar todas as instâncias de um rótulo específico.

Com esse gráfico, você pode comparar as curvas de precisão/recall de cada modelo para determinar qual deles tem uma relação aceitável entre a precisão e a recall conforme seu problema comercial específico. Esse gráfico mostra a média macro e micro da precisão/recall, além da precisão/recall associada a todas as classes de um modelo. 

**Macro – a média** computa a métrica independentemente de cada classe e, em seguida, usa a média, tratando todas as classes igualmente. No entanto, a **micro-Average** agrega as contribuições de todas as classes para calcular a média. Micro-Average é preferível se houver desequilíbrio de classe presente no conjunto de

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?
Dependendo do objetivo do problema comercial, a curva de recall de precisão ideal poderia ser diferente. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemplo 1: um modelo de classificação com baixa precisão e baixa recall
![Um modelo de classificação com baixa precisão e uma recall baixa](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemplo 2: um modelo de classificação com aproximadamente 100% de precisão e ~ 100% de recall 
![Um modelo de classificação alta precisão e RECALL](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>Gráfico ROC

A característica operacional do receptor (ou ROC) é uma plotagem dos rótulos corretamente classificados versus os rótulos classificados incorretamente para um modelo específico. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de valores com desequilíbrio de classe alta, pois a classe de maioria pode desafogar a contribuição de classes minoritárias.

Você pode visualizar a área sob o gráfico ROC como a proporção de amostras classificadas corretamente. Um usuário avançado do gráfico ROC pode parecer além da área sob a curva e obter uma intuição para as taxas reais positivos e falsos positivos como uma função do limite de classificação ou do limite de decisão.

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?
Uma curva ROC que se aproxima do canto superior esquerdo com a taxa positiva de 100% e a taxa de 0% de falsos positivos será o melhor modelo. Um modelo aleatório seria exibido como uma linha simples da parte inferior esquerda para o canto superior direito. Pior do que Random seria o DIP abaixo da linha y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exemplo 1: um modelo de classificação com baixos rótulos verdadeiros e grandes rótulos falsos
![Modelo de classificação com baixos rótulos verdadeiros e grandes rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exemplo 2: um modelo de classificação com alto rótulos verdadeiros e rótulos falsos inferiores

![um modelo de classificação com grandes rótulos verdadeiros e baixos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Gráfico de comparação de precisão

Os gráficos de comparação de precisão avaliam o desempenho dos modelos de classificação. Um gráfico de comparação de precisão mostra quantas vezes um modelo é melhor executado em comparação com um modelo aleatório. Isso proporciona um desempenho relativo que leva em conta o fato de que a classificação fica mais difícil à medida que você aumenta o número de classes. Um modelo aleatório prevê incorretamente uma fração mais alta de exemplos de um conjunto de um DataSet com dez classes comparadas a um conjunto de um DataSet com duas classes.

Você pode comparar a elevação do modelo criado automaticamente com Azure Machine Learning para a linha de base (modelo aleatório) para exibir a conquista do valor desse modelo específico.

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?

Um modelo de melhor desempenho terá uma curva de aumento superior no grafo e além da linha de base. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Exemplo 1: um modelo de classificação que é executado insatisfatoriamente em comparação com um modelo de seleção aleatório
![Um modelo de classificação que é pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemplo 2: um modelo de classificação que tem um desempenho melhor do que um modelo de seleção aleatório
![Um modelo de classificação que é executado melhor](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Gráfico de ganhos cumulativos

Um gráfico de ganhos cumulativos avalia o desempenho de um modelo de classificação por cada parte dos dados. Para cada percentil do conjunto de dados, o gráfico mostra quantas mais amostras foram classificadas com precisão em comparação com um modelo que está sempre incorreto. Essas informações fornecem outra maneira de observar os resultados no gráfico de comparação de precisão anexo.

O gráfico ganhos cumulativos ajuda você a escolher o corte de classificação usando uma porcentagem que corresponde a um ganho desejado do modelo. Você pode comparar o gráfico de ganhos cumulativos com a linha de base (modelo incorreto) para ver a porcentagem de amostras que foram classificadas corretamente em cada percentil de confiança.

#### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?

Semelhante a um gráfico de comparação de precisão, quanto mais alta a curva de ganhos cumulativos estiver acima da linha de base, melhor será o desempenho do seu modelo. Além disso, quanto mais perto sua curva de ganhos cumulativos for para o canto superior esquerdo do grafo, maior será o alcance do seu modelo em relação à linha de base. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemplo 1: um modelo de classificação com o mínimo de lucro
![um modelo de classificação com o mínimo de lucro](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemplo 2: um modelo de classificação com um lucro significativo
![Um modelo de classificação com um lucro significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Gráfico de calibragem

Um gráfico de calibragem exibe a confiança de um modelo de previsão. Ele faz isso mostrando a relação entre a probabilidade prevista e a probabilidade real, em que "probabilidade" representa a probabilidade de uma determinada instância pertencer a algum rótulo.

Em todos os problemas de classificação, você pode examinar a linha de calibração para cada média micro, macro e cada classe de um determinado modelo de previsão.

**Macro – a média** computa a métrica independentemente de cada classe e, em seguida, assume a média, tratando todas as classes igualmente. No entanto, a **micro-Average** agrega as contribuições de todas as classes para calcular a média. 

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?
Um modelo bem calibrado se alinha com a linha y = x, na qual ele prevê corretamente a probabilidade de que os exemplos pertençam a cada classe. Um modelo com excesso de confiança fará com que as probabilidades sejam próximas de zero e uma, raramente sem certeza sobre a classe de cada amostra.

#### <a name="example-1-a-well-calibrated-model"></a>Exemplo 1: um modelo bem calibrado
![ modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Exemplo 2: um modelo mais confiável
![Um modelo mais confiável](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Métricas de desempenho de regressão

A tabela a seguir resume as métricas de desempenho do modelo que o ML automatizado calcula para cada modelo de regressão ou previsão que é gerado para seu experimento. 

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|A variação explicada é a proporção na qual um modelo matemático responde pela variação de determinado conjunto de dados. É o percentual de redução na variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, ela é igual ao coeficiente de determinação (consulte r2_score abaixo).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R ^ 2 é o coeficiente de determinação ou a porcentagem de redução de erros quadrados em comparação com um modelo de linha de base que gera a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|Correlação de Spearman é uma medida não paramétrica da monotonicidade da relação entre dois conjuntos de dados. Diferentemente da correlação de Pearson, a correlação de Spearman não supõe que os dois conjuntos de dados estão distribuídos normalmente. Como outros coeficientes de correlação, esse varia entre -1 e +1 com 0 implicando nenhuma correlação. As correlações de -1 ou +1 implicam uma relação monotônica exata. Correlações positivas implicam que, à medida que x aumenta, y também. Correlações negativas implicam que, à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Erro médio absoluto é o valor esperado do valor absoluto da diferença entre a meta e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro médio absoluto normalizado é o Erro médio absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividir pelo intervalo dos dados|
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre a meta e a previsão. Essa perda é robusta para exceções.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro absoluto mediano normalizado é o Erro absoluto mediano dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_error|A raiz do erro quadrático médio é a raiz quadrada da diferença esperada ao quadrado entre a média e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|A raiz do erro quadrático médio normalizado é a raiz do erro quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_log_error|A raiz do erro de log quadrático médio é a raiz quadrada do erro logarítmico quadrático esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|A raiz do erro de log quadrático médio normalizada é a raiz do erro de log quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividir pelo intervalo dos dados|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Gráfico previsto versus verdadeiro

Previsto versus verdadeiro mostra a relação entre um valor previsto e seu valor verdadeiro correlacionado para um problema de regressão. 

Após cada execução, você pode ver um gráfico comparando os dados previstos e reais de cada modelo de regressão. Para proteger a privacidade dos dados, os valores ficam no mesmo compartimento, sendo que o tamanho de cada compartimento é mostrado como um gráfico de barras na parte inferior da área do gráfico. Você pode comparar o modelo de previsão, com as áreas de tonalidade mais leves, mostrando as margens de erro comparadas ao valor ideal de onde o modelo deveria estar.

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?
Esse grafo pode ser usado para medir o desempenho de um modelo à medida que mais próximo da linha y = x os valores previstos são, o melhor desempenho de um modelo de previsão.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Exemplo 1: um modelo de regressão com baixo desempenho
![Um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Exemplo 2: um modelo de regressão com alto desempenho
![Um modelo de regressão com alta precisão em suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Histograma do gráfico de resíduos

O ML automatizado fornece automaticamente um gráfico de resíduos para mostrar a distribuição de erros nas previsões de um modelo de regressão. Um resíduo é a diferença entre a previsão e o valor real ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Qual é a aparência de um modelo bom?
Para mostrar uma margem de erro com baixo ajuste, o histograma de resíduos deve ser moldado como uma curva de sino, centralizada em volta de zero.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemplo 1: um modelo de regressão com tendência em seus erros
![Modelo de regressão de SA com tendência em seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Exemplo 2: um modelo de regressão com uma distribuição mais uniforme de erros
![Um modelo de regressão com uma distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>Importância da interpretação do modelo e do recurso
O ML automatizado fornece um painel de interpretação de aprendizado de máquina para suas execuções.

Para obter mais informações sobre como habilitar recursos de interpretação, confira [interpretação: explicações de modelo no Machine Learning automatizado](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> O modelo ForecastTCN não tem suporte no momento pelo cliente de explicação. Esse modelo não retornará um painel de explicação se ele for retornado como o melhor modelo e não oferecer suporte a execuções de explicação sob demanda.

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [ML automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Experimente o [modelo de aprendizado de máquina automatizado explicação](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) dos notebooks de exemplo.