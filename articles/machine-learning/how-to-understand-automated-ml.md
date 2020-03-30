---
title: Entenda os resultados automatizados do ML
titleSuffix: Azure Machine Learning
description: Aprenda a visualizar e entender gráficos e métricas para cada uma de suas corridas automatizadas de aprendizado de máquina.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283453"
---
# <a name="understand-automated-machine-learning-results"></a>Entender os resultados de machine learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a visualizar e entender os gráficos e métricas de cada uma de suas corridas automatizadas de aprendizado de máquina. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e gráficos para modelos de regressão](#regression)
+ [Interpretância do modelo e importância de características](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Crie um experimento para sua corrida automatizada de aprendizado de máquina, seja com o SDK, ou no estúdio Azure Machine Learning.

    * Use o SDK para construir um [modelo de classificação](how-to-auto-train-remote.md) ou [modelo de regressão](tutorial-auto-train-models.md)
    * Use [o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-the-run"></a>Ver a corrida

Depois de executar um experimento automatizado de aprendizado de máquina, um histórico das corridas pode ser encontrado em seu espaço de trabalho de aprendizado de máquina. 

1. Vá até seu workspace.

1. No painel esquerdo do espaço de trabalho, selecione **Experimentos**.

   ![Captura de tela do menu de experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experimentos, selecione o que deseja explorar.

   [![Lista de experimentos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione a **Execução**.

   Execução de experimentos ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Nos Modelos, selecione o **nome algoritmo** para o modelo que você deseja explorar mais.

   [![Modelo do experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Você também vê esses mesmos resultados `RunDetails`durante uma corrida quando você usa o [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Resultados de classificação

As seguintes métricas e gráficos estão disponíveis para cada modelo de classificação que você constrói usando os recursos automatizados de aprendizado de máquina do Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de precisão-recall](#precision-recall-chart)
+ [ROC (características operacionais do destinatário)](#roc)
+ [Curva de comparação de precisão](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Gráfico de calibragem](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As seguintes métricas são salvas em cada iteração de execução para uma tarefa de classificação.

Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--
AUC_Macro| AUC é a área embaixo da Curva característica operacional do receptor. Macro é a média aritmética do AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC é a área embaixo da Curva característica operacional do receptor. A micro é computada globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC é a área embaixo da Curva característica operacional do receptor. Ponderada é a média aritmética da pontuação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy é o percentual de rótulos previstos que coincidem exatamente com os rótulos verdadeiros. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Macro é a média aritmética da pontuação média de precisão de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. A micro é computada globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Ponderada é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Precisão equilibrada é a média aritmética do recolhimento de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Pontuação F1 é a média harmônica de precisão e recuperação. Macro é a média aritmética da pontuação da F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Pontuação F1 é a média harmônica de precisão e recuperação. Micro é computado globalmente contando o total de verdadeiros positivos, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Pontuação F1 é a média harmônica de precisão e recuperação. Média ponderada por frequência de classe ou pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Esta é a função de perda usada na regressão logística (multinomial) e extensões dela, como redes neurais, definida como a probabilidade de registro negativo dos rótulos verdadeiros dadas as previsões de um classificador probabilístico. Para uma única amostra com {0,1} etiqueta verdadeira yt in e probabilidade estimada yp que yt = 1, a perda de log é -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|O Recolhimento de macro normalizado é o recolhimento de macro normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isso é alcançado por norm_macro_recall := (recall_score_macro - R)/(1 - R), onde R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0.5 para classificação binária e R=(1/C) para problemas de classificação de classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro" |
precision_score_macro|Precisão é o percentual de elementos positivamente previstos que são corretamente rotulados. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precisão é o percentual de elementos positivamente previstos que são corretamente rotulados. Micro é computado globalmente contando o total de verdadeiros positivos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precisão é o percentual de elementos positivamente previstos que são corretamente rotulados. Ponderada é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall é o percentual de elementos corretamente rotulados de uma determinada classe. Macro é a média aritmética de recordação para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall é o percentual de elementos corretamente rotulados de uma determinada classe. Micro é computado globalmente contando o total de verdadeiros positivos, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall é o percentual de elementos corretamente rotulados de uma determinada classe. Ponderado é a média aritmética de recordação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Precisão ponderada é a precisão onde o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na verdadeira classe desse exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no destino|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matriz de confusão

#### <a name="what-is-a-confusion-matrix"></a>O que é uma matriz de confusão?
Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha exibe as instâncias da classe verdadeira ou real em seu conjunto de dados, e cada coluna representa as instâncias da classe que foi prevista pelo modelo. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>O que o ML automatizado faz com a matriz de confusão?
Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo compilado. Para cada matriz de confusão, o ML automatizado mostrará a freqüência de cada rótulo previsto (coluna) em comparação com o rótulo verdadeiro (linha). Quanto mais escura a cor, maior a contagem nessa parte específica da matriz. 

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Estamos comparando o valor real do conjunto de dados com os valores previstos que o modelo deu. Por causa disso, os modelos de aprendizado de máquina têm maior precisão se o modelo tiver a maioria de seus valores ao longo da diagonal, o que significa que o modelo previu o valor correto. Se um modelo tiver desequilíbrio de classe, a matriz de confusão ajudará a detectar um modelo tendencioso.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemplo 1: Um modelo de classificação com pouca precisão
![Um modelo de classificação com baixa precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemplo 2: Um modelo de classificação com alta precisão 
![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemplo 3: Um modelo de classificação com alta precisão e alto viés nas previsões do modelo
![Um modelo de classificação com alta precisão e alto viés nas previsões do modelo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Gráfico de precisão/recall
#### <a name="what-is-a-precision-recall-chart"></a>O que é um gráfico de recordação de precisão?
A curva de recordação de precisão mostra a relação entre precisão e recordação de um modelo. O termo precisão representa essa capacidade de um modelo rotular todas as instâncias corretamente. O termo “recall” representa a capacidade de um classificador localizar todas as instâncias de um rótulo específico.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>O que o ML automatizado faz com o gráfico de recall de precisão?

Com esse gráfico, você pode comparar as curvas de precisão/recall de cada modelo para determinar qual deles tem uma relação aceitável entre a precisão e a recall conforme seu problema comercial específico. Esse gráfico mostra a média macro e micro da precisão/recall, além da precisão/recall associada a todas as classes de um modelo. 

Macro-média calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes igualmente. No entanto, a micromédia agregará as contribuições de todas as classes para calcular a média. A micromédia é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Dependendo do objetivo do problema do negócio, a curva de precisão ideal pode diferir. Alguns exemplos são dados abaixo

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemplo 1: Um modelo de classificação com baixa precisão e baixa recuperação
![Um modelo de classificação com baixa precisão e baixa recordação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemplo 2: Um modelo de classificação com ~100% precisão e ~100% recall 
![Um modelo de classificação de alta precisão e recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Gráfico ROC

#### <a name="what-is-a-roc-chart"></a>O que é um gráfico ROC?
O ROC (característica operacional do receptor) é um gráfico que compara os rótulos classificados de modo correto e incorreto de um modelo específico. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de dados com alto desvio, pois ela não mostrará os rótulos falso-positivos.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>O que o ML automatizado faz com o gráfico ROC?
O ML automatizado gera o Macro Average Precision-Recall, o Micro Average Precision-Recall e o recall de precisão associado a todas as classes para um modelo. 

Macro-média calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes igualmente. No entanto, a micromédia agregará as contribuições de todas as classes para calcular a média. A micromédia é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Idealmente, o modelo terá uma taxa positiva 100% verdadeira e mais próxima de 0% de taxa falsa positiva. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exemplo 1: Um modelo de classificação com etiquetas verdadeiras baixas e etiquetas falsas altas
![Modelo de classificação com etiquetas verdadeiras baixas e etiquetas falsas altas](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exemplo 2: Um modelo de classificação com etiquetas verdadeiras altas e etiquetas falsas baixas
![um modelo de classificação com rótulos verdadeiros elevados e rótulos falsos baixos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Gráfico de comparação de precisão
#### <a name="what-is-a-lift-chart"></a>O que é um gráfico de elevação?
Gráficos de comparação de precisão são usados para avaliar o desempenho de um modelo de classificação. Ele mostra o quanto você pode esperar fazer com o modelo gerado em comparação com sem um modelo em termos de precisão.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>O que o ML automatizado faz com o gráfico de elevação?
Você pode comparar a precisão do modelo compilado automaticamente do Azure Machine Learning com a linha de base para exibir o ganho de valor desse determinado modelo.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exemplo 1: Um modelo de classificação que faz pior do que um modelo de seleção aleatória
![Um modelo de classificação que faz pior do que um modelo de seleção aleatória](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemplo 2: Um modelo de classificação que tem um desempenho melhor do que um modelo de seleção aleatória
![Um modelo de classificação que tem melhor desempenho](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Gráfico de ganhos
#### <a name="what-is-a-gains-chart"></a>O que é um gráfico de ganhos?

Um gráfico de ganhos avalia o desempenho de um modelo de classificação em cada porção dos dados. Ele mostra, para cada percentil do conjunto de dados, o quão melhor o desempenho pode ser em comparação a um modelo de seleção aleatória.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>O que o ML automatizado faz com o gráfico de ganhos?
Use o gráfico de ganhos cumulativos para ajudar você a escolher o corte de classificação usando uma porcentagem que corresponda a um ganho desejado do modelo. Essas informações fornecem outra maneira de observar os resultados no gráfico de comparação de precisão anexo.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemplo 1: Um modelo de classificação com ganho mínimo
![um modelo de classificação com ganho mínimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemplo 2: Um modelo de classificação com ganho significativo
![Um modelo de classificação com ganho significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Gráfico de calibração

#### <a name="what-is-a-calibration-chart"></a>O que é um gráfico de calibração?
Um gráfico de calibragem é usado para exibir a confiança de um modelo de previsão. Ele faz isso mostrando a relação entre a probabilidade prevista e a probabilidade real, onde "probabilidade" representa a probabilidade de que uma instância específica pertence a algum rótulo.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>O que o ML automatizado faz com o gráfico de calibração?
Em todos os problemas de classificação, você pode examinar a linha de calibração para cada média micro, macro e cada classe de um determinado modelo de previsão.

Macro-média calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes igualmente. No entanto, a micromédia agregará as contribuições de todas as classes para calcular a média. 
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
 Um modelo bem calibrado se alinha com a linha y=x, onde está razoavelmente confiante em suas previsões. Um modelo com confiança excessiva se nivela à linha y=0, na qual a probabilidade prevista está presente, mas não há nenhuma probabilidade real. 


##### <a name="example-1-a-well-calibrated-model"></a>Exemplo 1: Um modelo bem calibrado
![ modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exemplo 2: Um modelo super confiante
![Um modelo super confiante](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Resultados de regressão

As seguintes métricas e gráficos estão disponíveis para cada modelo de regressão que você constrói usando os recursos automatizados de aprendizado de máquina do Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Previsto vs. True](#pvt)
+ [Histograma de resíduos](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Métricas de regressão

As seguintes métricas são salvas em cada iteração de execução para uma tarefa de regressão ou previsão.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|A variação explicada é a proporção na qual um modelo matemático responde pela variação de determinado conjunto de dados. É o percentual de redução na variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, ela é igual à variação explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R2 é o coeficiente de determinação ou a redução de percentual em erros quadráticos, comparado a um modelo de linha de base que gera a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|Correlação de Spearman é uma medida não paramétrica da monotonicidade da relação entre dois conjuntos de dados. Diferentemente da correlação de Pearson, a correlação de Spearman não supõe que os dois conjuntos de dados estão distribuídos normalmente. Como outros coeficientes de correlação, esse varia entre -1 e +1 com 0 implicando nenhuma correlação. As correlações de -1 ou +1 implicam uma relação monotônica exata. Correlações positivas implicam que, à medida que x aumenta, y também. Correlações negativas implicam que, à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Erro médio absoluto é o valor esperado do valor absoluto da diferença entre a meta e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro médio absoluto normalizado é o Erro médio absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividir pelo intervalo dos dados|
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre a meta e a previsão. Essa perda é robusta para exceções.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro absoluto mediano normalizado é o Erro absoluto mediano dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_error|A raiz do erro quadrático médio é a raiz quadrada da diferença esperada ao quadrado entre a média e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|A raiz do erro quadrático médio normalizado é a raiz do erro quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_log_error|A raiz do erro de log quadrático médio é a raiz quadrada do erro logarítmico quadrático esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|A raiz do erro de log quadrático médio normalizada é a raiz do erro de log quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividir pelo intervalo dos dados|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Gráfico previsto vs. True
#### <a name="what-is-a-predicted-vs-true-chart"></a>O que é um gráfico Predito vs. True?
Predito vs. True mostra a relação entre um valor previsto e seu valor real correlacionante para um problema de regressão. Esse gráfico pode ser usado para medir o desempenho de um modelo que, quanto mais próximos à linha y=x linha estiverem seus valores previstos, melhor será a precisão de um modelo de previsão.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>O que o ML automatizado faz com o gráfico Predicted vs. True?
Após cada execução, você pode ver um gráfico comparando os dados previstos e reais de cada modelo de regressão. Para proteger a privacidade dos dados, os valores ficam no mesmo compartimento, sendo que o tamanho de cada compartimento é mostrado como um gráfico de barras na parte inferior da área do gráfico. Você pode comparar o modelo de previsão, com as áreas de tonalidade mais leves, mostrando as margens de erro comparadas ao valor ideal de onde o modelo deveria estar.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exemplo 1: Um modelo de classificação com baixa precisão
![Um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exemplo 2: Um modelo de regressão com alta precisão 
[![Um modelo de regressão com alta precisão em suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Gráfico de histograma de resíduos
#### <a name="what-is-a-residuals-chart"></a>O que é um gráfico de resíduos?
Um resíduo representa um y observado, que é o y previsto. Para mostrar uma margem de erro com baixo desvio, o histograma de resíduos deve ter a forma de uma curva de sino, centralizada em torno do 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>O que o ML automatizado faz com o gráfico de resíduos?
O ML automatizado fornece automaticamente um gráfico de resíduos para mostrar a distribuição de erros nas previsões.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Um bom modelo normalmente terá uma curva de sino ou erros em torno de zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemplo 1: Um modelo de regressão com viés em seus erros
![Modelo de regressão SA com viés em seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exemplo 2: Um modelo de regressão com distribuição mais uniforme de erros
![Um modelo de regressão com distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Interpretância do modelo e importância de características
O ML automatizado fornece um painel de interpretação de aprendizado de máquina para suas corridas.
Para obter mais informações sobre como habilitar recursos de interpretação, consulte [o como-fazer](how-to-machine-learning-interpretability-automl.md) na habilitação da interpretável em experimentos ml automatizados.

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Experimente os cadernos de exemplo [de modelo de aprendizagem de máquina automatizados.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
