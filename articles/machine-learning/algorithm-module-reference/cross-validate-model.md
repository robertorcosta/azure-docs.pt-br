---
title: 'Modelo de validação cruzada: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Cross-Validate Model no Azure Machine Learning para validar as estimativas de parâmetros para modelos de classificação ou regressão particionando os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477639"
---
# <a name="cross-validate-model"></a>Modelo de validação cruzada

Este artigo descreve como usar o módulo Cross Validate Model no azure Machine Learning designer (visualização). *A validação cruzada* é uma técnica frequentemente usada no aprendizado de máquina para avaliar tanto a variabilidade de um conjunto de dados quanto a confiabilidade de qualquer modelo treinado através desses dados.  

O módulo Cross Validate Model toma como entrada um conjunto de dados rotulado, juntamente com um modelo de classificação ou regressão não treinado. Ele divide o conjunto de dados em algum número de*subconjuntos (dobras),* constrói um modelo em cada dobra e, em seguida, retorna um conjunto de estatísticas de precisão para cada dobra. Comparando as estatísticas de precisão para todas as dobras, você pode interpretar a qualidade do conjunto de dados. Você pode então entender se o modelo é suscetível a variações nos dados.  

Cross Validate Model também retorna os resultados e probabilidades previstos para o conjunto de dados, para que você possa avaliar a confiabilidade das previsões.  

### <a name="how-cross-validation-works"></a>Como funciona a validação cruzada

1. A validação cruzada divide aleatoriamente os dados de treinamento em dobras. 

   O algoritmo padroniza para 10 dobras se anteriormente você não particionou do conjunto de dados. Para dividir o conjunto de dados em um número diferente de dobras, você pode usar o módulo [Partição e Amostra](partition-and-sample.md) e indicar quantas dobras usar.  

2.  O módulo reserva os dados na dobra 1 para serem usados para validação. (Isso às vezes é chamado de *dobra de recompor*.) O módulo usa as dobras restantes para treinar um modelo. 

    Por exemplo, se você criar cinco dobras, o módulo gerará cinco modelos durante a validação cruzada. O módulo treina cada modelo usando quatro quintos dos dados. Ele testa cada modelo no 1/5 restante.  

3.  Durante o teste do modelo para cada dobra, o módulo avalia múltiplas estatísticas de precisão. Quais estatísticas o módulo usa depende do tipo de modelo que você está avaliando. Diferentes estatísticas são utilizadas para avaliar modelos de classificação versus modelos de regressão.  

4.  Quando o processo de construção e avaliação é concluído para todas as dobras, o Cross Validate Model gera um conjunto de métricas de desempenho e resultados pontuados para todos os dados. Revise essas métricas para ver se qualquer dobra única tem alta ou baixa precisão. 

### <a name="advantages-of-cross-validation"></a>Vantagens da validação cruzada

Uma maneira diferente e comum de avaliar um modelo é dividir os dados em um conjunto de treinamento e teste usando [Dados Divididos](split-data.md)e, em seguida, validar o modelo nos dados de treinamento. Mas a validação cruzada oferece algumas vantagens:  

-   A validação cruzada usa mais dados de teste.

    A validação cruzada mede o desempenho do modelo com os parâmetros especificados em um espaço de dados maior. Ou seja, a validação cruzada usa todo o conjunto de dados de treinamento para treinamento e avaliação, em vez de uma parte. Em contraste, se você validar um modelo usando dados gerados a partir de uma divisão aleatória, normalmente você avalia o modelo em apenas 30% ou menos dos dados disponíveis.  

    No entanto, como a validação cruzada treina e valida o modelo várias vezes em um conjunto de dados maior, é muito mais intensivo computacionalmente. Leva muito mais tempo do que validar em uma divisão aleatória.  

-   A validação cruzada avalia tanto o conjunto de dados quanto o modelo.

    A validação cruzada não mede simplesmente a precisão de um modelo. Ele também dá uma ideia de quão representativo o conjunto de dados é e quão sensível o modelo pode ser para variações nos dados.  

## <a name="how-to-use-cross-validate-model"></a>Como usar o modelo de validação cruzada

A validação cruzada pode levar muito tempo para ser executada se o conjunto de dados for grande.  Assim, você pode usar cross validate model na fase inicial de construção e teste do seu modelo. Nessa fase, você pode avaliar a bondade dos parâmetros do modelo (assumindo que o tempo de computação é tolerável). Em seguida, você pode treinar e avaliar o seu modelo usando os parâmetros estabelecidos com os módulos [Modelo de Trem](train-model.md) e Avaliar [Modelo.](evaluate-model.md)

Neste cenário, você treina e testa o modelo usando o Cross Validate Model.

1. Adicione o módulo Cross Validate Model ao seu pipeline. Você pode encontrá-lo no designer azure Machine Learning, na categoria **Avaliação de & de Pontuação de Modelo.** 

2. Conecte a saída de qualquer modelo de classificação ou regressão. 

    Por exemplo, se você estiver usando a árvore de **decisão duas classes aumentada** para classificação, configure o modelo com os parâmetros que deseja. Em seguida, arraste um conector da porta **modelo não treinada** do classificador para a porta correspondente do Modelo de Validação Cruzada. 

    > [!TIP] 
    > Você não precisa treinar o modelo, porque o Cross-Validate Model treina automaticamente o modelo como parte da avaliação.  
3.  Na porta **Dataset** do Cross Validate Model, conecte qualquer conjunto de dados de treinamento rotulado.  

4.  No painel direito do Modelo de Validação Cruzada, clique em **Editar coluna**. Selecione a única coluna que contém o rótulo de classe ou o valor previsível. 

5. Defina um valor para o parâmetro **de sementes aleatória** se você quiser repetir os resultados da validação cruzada em sucessivas corridas nos mesmos dados.  

6. Envie o oleoduto.

7. Consulte a seção [Resultados](#results) para obter uma descrição dos relatórios.

## <a name="results"></a>Resultados

Depois que todas as iterações são concluídas, o Cross Validate Model cria pontuações para todo o conjunto de dados. Ele também cria métricas de desempenho que você pode usar para avaliar a qualidade do modelo.

### <a name="scored-results"></a>Resultados classificados

A primeira saída do módulo fornece os dados de origem para cada linha, juntamente com alguns valores previstos e probabilidades relacionadas. 

Para visualizar os resultados, no pipeline, clique com o botão direito do mouse no módulo Cross Validate Model. Selecione **Visualizar resultados pontuados**.

| Nome da nova coluna      | Descrição                              |
| -------------------- | ---------------------------------------- |
| Rótulos classificados        | Esta coluna é adicionada no final do conjunto de dados. Ele contém o valor previsto para cada linha. |
| Probabilidades pontuadas | Esta coluna é adicionada no final do conjunto de dados. Indica a probabilidade estimada do valor em **Rótulos Pontuados**. |
| Número da dobra          | Indica o índice baseado em zero da dobra a que cada linha de dados foi atribuída durante a validação cruzada. |

 ### <a name="evaluation-results"></a>Resultados de avaliação

O segundo relatório é agrupado por dobras. Lembre-se que durante a execução, o Cross Validate Model divide aleatoriamente os dados de treinamento em *n* dobras (por padrão, 10). Em cada iteração sobre o conjunto de dados, o Cross Validate Model usa uma dobra como conjunto de dados de validação. Ele usa as dobras *n-1* restantes para treinar um modelo. Cada um dos modelos *n* é testado contra os dados em todas as outras dobras.

Neste relatório, as dobras são listadas por valor de índice, em ordem crescente.  Para encomendar em qualquer outra coluna, você pode salvar os resultados como um conjunto de dados.

Para visualizar os resultados, no pipeline, clique com o botão direito do mouse no módulo Cross Validate Model. Selecione **Visualizar resultados de avaliação por dobra**.


|Nome da coluna| Descrição|
|----|----|
|Número de dobra| Um identificador para cada dobra. Se você criasse cinco dobras, haveria cinco subconjuntos de dados, numerados de 0 a 4.
|Número de exemplos em dobra|O número de linhas atribuídas a cada dobra. Eles devem ser aproximadamente iguais. |


O módulo também inclui as seguintes métricas para cada dobra, dependendo do tipo de modelo que você está avaliando: 

+ **Modelos de classificação**: Precisão, recall, pontuação F, AUC, precisão  

+ **Modelos de regressão**: Erro absoluto médio, erro quadrado da raiz, erro absoluto relativo, erro quadrado relativo e coeficiente de determinação


## <a name="technical-notes"></a>Observações técnicas  

+ É uma prática recomendada para normalizar conjuntos de dados antes de usá-los para validação cruzada. 

+ Cross Validate Model é muito mais intensivo computacionalmente e leva mais tempo para ser concluído do que se você validasse o modelo usando um conjunto de dados dividido aleatoriamente. A razão é que o Cross Validate Model treina e valida o modelo várias vezes.

+ Não há necessidade de dividir o conjunto de dados em conjuntos de treinamento e testes quando você usa validação cruzada para medir a precisão do modelo. 


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

