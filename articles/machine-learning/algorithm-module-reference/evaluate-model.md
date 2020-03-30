---
title: 'Avaliar modelo: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Avaliar modelo no Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456397"
---
# <a name="evaluate-model-module"></a>Avaliar módulo modelo

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para medir a precisão de um modelo treinado. Você fornece um conjunto de dados contendo pontuações geradas a partir de um modelo, e o módulo **Modelo de avaliação** calcula um conjunto de métricas de avaliação padrão do setor.
  
 As métricas retornadas pelo **Modelo de Avaliação** dependem do tipo de modelo que você está avaliando:  
  
-   **Modelos de Classificação**    
-   **Modelos de regressão**  
-   **Modelos de clustering**  


> [!TIP]
> Se você é novo na avaliação de modelos, recomendamos a série de vídeos do Dr. Stephen Elston, como parte do curso de [aprendizagem](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) de máquina da EdX. 


Existem três maneiras de usar o módulo **Modelo de Avaliação:**

+ Gere pontuações sobre seus dados de treinamento e avalie o modelo com base nessas pontuações
+ Gerar pontuações no modelo, mas comparar essas pontuações com pontuações em um conjunto de testes reservado
+ Compare pontuações para dois modelos diferentes, mas relacionados, usando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Use os dados de treinamento

Para avaliar um modelo, você deve se conectar a um conjunto de dados que contém um conjunto de colunas de entrada e classificações.  Se nenhum outro dado estiver disponível, você pode usar seu conjunto de dados original.

1. Conecte a saída do conjunto de **dados Marcado** do Modelo [de Pontuação](./score-model.md) à entrada do Modelo **de Avaliação**. 
2. Clique **em Avaliar o** módulo Modelo e execute o pipeline para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Use dados de teste

Um cenário comum no aprendizado de máquina é separar seu conjunto de dados original em conjuntos de dados de treinamento e teste, usando o módulo [Split](./split-data.md) ou o módulo [Partição e Amostra.](./partition-and-sample.md) 

1. Conecte a saída do conjunto de **dados Marcado** do Modelo [de Pontuação](score-model.md) à entrada do Modelo **de Avaliação**. 
2. Conecte a saída do módulo Split Data que contém os dados de teste à entrada à direita do **Modelo de Avaliação**.
2. Clique **em Avaliar o** módulo modelo e selecione Executar **selecionado** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Compare as pontuações de dois modelos

Você também pode conectar um segundo conjunto de pontuações para **avaliar modelo**.  Os escores podem ser um conjunto de avaliação compartilhada que tenha resultados conhecidos, ou um conjunto de resultados de um modelo diferente para os mesmos dados.

This featureEste recurso é útil pois você pode comparar facilmente resultados de dois modelos diferentes nos mesmos dados. Ou, você pode comparar classificações de duas execuções diferentes sobre os mesmos dados com parâmetros diferentes.

1. Conecte a saída do conjunto de **dados Marcado** do Modelo [de Pontuação](score-model.md) à entrada do Modelo **de Avaliação**. 
2. Conecte a saída do módulo Score Model para o segundo modelo à entrada à direita do **Modelo de Avaliação**.
3. Envie o oleoduto.

## <a name="results"></a>Resultados

Depois de executar **O Modelo de Avaliação,** clique com o botão direito do mouse no módulo e selecione **Visualizar resultados de avaliação** para ver os resultados.

Se você conectar conjuntos de dados a ambas as entradas do **Modelo de Avaliação,** os resultados conterão métricas para ambos os conjuntos de dados ou ambos os modelos.
O modelo ou dados anexados à porta esquerda são apresentados primeiro no relatório, seguidos das métricas para o conjunto de dados, ou modelo anexado na porta direita.  

Por exemplo, a imagem a seguir representa uma comparação dos resultados de dois modelos de clustering que foram construídos sobre os mesmos dados, mas com parâmetros diferentes.  

![Comparando2Modelos](media/module/evaluate-2-models.png)  

Por se trata de um modelo de clustering, os resultados da avaliação são diferentes se você comparar os escores de dois modelos de regressão, ou comparar dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta seção descreve as métricas retornadas para os tipos específicos de modelos suportados para uso com **Modelo de Avaliação**:

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)
+ [modelos de agrupamento](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação

As seguintes métricas são relatadas ao avaliar modelos de classificação.
  
-   **A precisão** mede a bondade de um modelo de classificação como a proporção de resultados verdadeiros ao total de casos.  
  
-   **Precisão** é a proporção de resultados verdadeiros sobre todos os resultados positivos.  
  
-   **Recall** é a fração de todos os resultados corretos devolvidos pelo modelo.  
  
-   **A pontuação F** é calculada como a média ponderada de precisão e recall entre 0 e 1, onde o valor ideal de pontuação F é 1.  
  
-   **AUC** mede a área a curva traçada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Esta métrica é útil porque fornece um único número que permite comparar modelos de diferentes tipos.  
  
- **A perda média de log** é uma pontuação única usada para expressar a penalidade por resultados errados. É calculada como a diferença entre duas distribuições de probabilidades – a verdadeira e a do modelo.  
  
- **A perda de registro** de treinamento é uma pontuação única que representa a vantagem do classificador sobre uma previsão aleatória. A perda de log mede a incerteza do seu modelo, comparando as probabilidades que ele produz com os valores conhecidos (verdade de solo) nos rótulos. Você quer minimizar a perda de log para o modelo como um todo.

### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas devolvidas para modelos de regressão são projetadas para estimar a quantidade de erro.  Considera-se que um modelo se encaixa bem nos dados se a diferença entre valores observados e previstos for pequena. No entanto, olhar para o padrão dos resíduos (a diferença entre qualquer ponto previsto e seu valor real correspondente) pode dizer muito sobre o viés potencial no modelo.  
  
 As seguintes métricas são relatadas para avaliação de modelos de regressão.
  
- **O erro absoluto médio (MAE)** mede o quão próximos são as previsões dos resultados reais; assim, uma pontuação menor é melhor.  
  
- **Root mean squared error (RMSE)** cria um único valor que resume o erro no modelo. Ao esquartejar a diferença, a métrica desconsidera a diferença entre a superprevisão e a sub-previsão.  
  
- **Erro absoluto relativo (RAE)** é a diferença relativa absoluta entre valores esperados e reais; relativo porque a diferença média é dividida pela média aritmética.  
  
- **O erro quadrado relativo (RSE)** normaliza da mesma forma o erro total quadrado dos valores previstos, dividindo-se pelo erro total quadrado dos valores reais.  
  

  
- **O coeficiente de determinação**, muitas vezes referido como R<sup>2</sup>, representa o poder preditivo do modelo como valor entre 0 e 1. Zero significa que o modelo é aleatório (não explica nada); 1 significa que há um ajuste perfeito. No entanto, deve-se ter cuidado na interpretação dos valores de R<sup>2,</sup> pois valores baixos podem ser totalmente normais e valores elevados podem ser suspeitos.

###  <a name="metrics-for-clustering-models"></a>Métricas para modelos de clustering

Como os modelos de clustering diferem significativamente dos modelos de classificação e regressão em muitos aspectos, [o Modelo de Avaliação](evaluate-model.md) também retorna um conjunto diferente de estatísticas para modelos de clustering.  
  
 As estatísticas retornadas para um modelo de clustering descrevem quantos pontos de dados foram atribuídos a cada cluster, a quantidade de separação entre clusters e o quão fortemente os pontos de dados são agrupados dentro de cada cluster.  
  
 As estatísticas para o modelo de clustering são médias sobre todo o conjunto de dados, com linhas adicionais contendo as estatísticas por cluster.  
  
As seguintes métricas são relatadas para avaliação de modelos de clustering.
    
-   As pontuações na coluna, **Distância Média para Outro Centro,** representam o quão perto, em média, cada ponto no cluster está dos centróides de todos os outros clusters.   

-   As pontuações na coluna, **Distância Média para Centro de Cluster,** representam a proximidade de todos os pontos em um cluster com o centróide desse cluster.  
  
-   A coluna **Número de Pontos** mostra quantos pontos de dados foram atribuídos a cada cluster, juntamente com o número total total de pontos de dados em qualquer cluster.  
  
     Se o número de pontos de dados atribuídos a clusters for menor do que o número total de pontos de dados disponíveis, significa que os pontos de dados não poderiam ser atribuídos a um cluster.  
  
-   As pontuações na coluna, **Distância Máxima para Centro de Cluster,** representam a soma das distâncias entre cada ponto e o centróide do cluster desse ponto.  
  
     Se esse número for alto, pode significar que o cluster está amplamente disperso. Você deve rever esta estatística juntamente com o **Centro de Distância Média para Cluster** para determinar o spread do cluster.   

-   A pontuação **de Avaliação Combinada** na parte inferior de cada seção de resultados lista as pontuações médias dos clusters criados nesse modelo específico.  
  

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 