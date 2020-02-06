---
title: 'Designer: exemplo de previsão de preços de automóveis (básico)'
titleSuffix: Azure Machine Learning
description: Crie um modelo de regressão de ML para prever o preço de um automóvel sem escrever uma só linha de código com o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 6f4fe941cc44211f9f5d5e77b11043257b43a8ea
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963279"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Use a regressão para prever os preços de automóveis com o designer do Azure Machine Learning

**Amostra 1 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um modelo de regressão de aprendizado de máquina sem escrever uma só linha de código usando o designer (versão prévia).

Este pipeline treina um **regressor linear** para prever o preço de um automóvel com base em recursos técnicos, como marca, modelo, potência e tamanho. Como você está tentando responder à pergunta "Quanto?", isso é chamado de problema de regressão. No entanto, você pode aplicar as mesmas etapas fundamentais neste exemplo para resolver qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering etc.

As etapas fundamentais de um modelo de machine learning de treinamento são:

1. Obter os dados
1. Pré-processar os dados
1. Treinar o modelo
1. Avalie o modelo

Este é o grafo final e concluído do pipeline. Este artigo fornece a justificativa de todos os módulos, de modo que você possa tomar decisões semelhantes por conta própria.

![Grafo do pipeline](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 1 para abri-la.


## <a name="get-the-data"></a>Obter os dados

Esta amostra usa o conjunto de dados **Dados de preços de automóveis (Bruto)** , que pertence ao Repositório de Aprendizado de Máquina do UCI. O conjunto de dados contém 26 colunas que trazem informações sobre automóveis, incluindo marca, modelo, preço e características do veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro. O objetivo desta amostra é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou compartimentalização dos dados. No designer, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo **Transformação de Dados** no painel esquerdo.

Use o módulo **Selecionar Colunas no Conjunto de Dados** para excluir as perdas normalizadas que tenham muitos valores ausentes. Em seguida, use **Limpar Dados Ausentes** para remover as linhas que tenham valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Treinar o modelo

Os problemas de aprendizado de máquina variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisará ajustar os parâmetros para treinar um modelo mais preciso. Em seguida, precisará avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo desta amostra é prever os preços de automóveis e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear. Portanto, usamos a **Regressão de Floresta de Decisão** para esse pipeline.

Use o módulo **Dividir Dados** para dividir aleatoriamente os dados de entrada, de modo que o conjunto de dados de treinamento contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% dos dados originais.

## <a name="test-evaluate-and-compare"></a>Teste, avaliação e comparação

Divida o conjunto de dados e use conjuntos de dados diferentes para treinar e testar o modelo a fim de tornar a avaliação do modelo mais objetiva.

Depois que o modelo for treinado, você poderá usar os módulos **Pontuar Modelo** e **Avaliar Modelo** para gerar os resultados previstos e avaliar os modelos.

O módulo **Pontuar Modelo** gera previsões para o conjunto de dados de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída de **Pontuar Modelo** e, em seguida, selecione **Visualizar**.

![Pontuar o resultado](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Transmita as pontuações ao módulo **Avaliar Modelo** para gerar métricas de avaliação. Para verificar o resultado, selecione a porta de saída de **Avaliar Modelo** e, em seguida, selecione **Visualizar**.

![Avaliar o resultado](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 2: regressão: comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3: classificação com seleção de recursos: previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5: classificação: prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 6: classificação: prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7: classificação de textos: conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)