---
title: 'Designer: exemplo de previsão de preços de automóveis (avançado)'
titleSuffix: Azure Machine Learning
description: Crie e compare vários modelos de regressão de ML para prever o preço de um automóvel baseado em recursos técnicos com o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 189b36c8d9a7c1d1e247bb2cb34795dc4e3595ce
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963335"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Treinar e comparar vários modelos de regressão para prever os preços de automóveis com o designer do Azure Machine Learning

**Amostra 2 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um pipeline de aprendizado de máquina sem escrever uma só linha de código usando o designer (versão prévia). Esta amostra treina e compara vários modelos de regressão para prever o preço de um automóvel baseado nos recursos técnicos. Forneceremos a justificativa para as escolhas feitas nesse pipeline, de modo que você possa resolver os próprios problemas de aprendizado de máquina.

Se estiver começando a usar o aprendizado de máquina, confira a [versão básica](how-to-designer-sample-regression-automobile-price-basic.md) desse pipeline.

Este é o grafo concluído deste pipeline:

[![Grafo do pipeline](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 2 para abri-la. 

## <a name="pipeline-summary"></a>Resumo do pipeline

Use as seguintes etapas para criar o pipeline de aprendizado de máquina:

1. Obter os dados.
1. Pré-processar os dados.
1. Treinar o modelo.
1. Testar, avaliar e comparar os modelos.

## <a name="get-the-data"></a>Obter os dados

Esta amostra usa o conjunto de dados **Dados de preços de automóveis (Bruto)** , que pertence ao Repositório de Aprendizado de Máquina do UCI. Este conjunto de dados contém 26 colunas que trazem informações sobre automóveis, incluindo marca, modelo, preço e características do veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou compartimentalização dos dados. No designer, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo **Transformação de Dados** no painel esquerdo.

Use o módulo **Selecionar Colunas no Conjunto de Dados** para excluir as perdas normalizadas que tenham muitos valores ausentes. Em seguida, usamos **Limpar Dados Ausentes** para remover as linhas que tenham valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Treinar o modelo

Os problemas de aprendizado de máquina variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisará ajustar os parâmetros para treinar um modelo mais preciso. Em seguida, precisará avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo deste pipeline é prever os preços de automóveis e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear.

Para comparar o desempenho de diferentes algoritmos, usamos dois algoritmos não lineares, **Regressão de Árvore de Decisão Aumentada** e **Regressão de Floresta de Decisão** para criar modelos. Ambos os algoritmos têm parâmetros que podem ser alterados, mas esta amostra usa os valores padrão para este pipeline.

Use o módulo **Dividir Dados** para dividir aleatoriamente os dados de entrada, de modo que o conjunto de dados de treinamento contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% dos dados originais.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e comparar os modelos

Você usará dois conjuntos diferentes de dados escolhidos aleatoriamente para treinar e testar o modelo, conforme descrito na seção anterior. Divida o conjunto de dados e use conjuntos de dados diferentes para treinar e testar o modelo a fim de tornar a avaliação do modelo mais objetiva.

Depois que o modelo for treinado, use os módulos **Pontuar Modelo** e **Avaliar Modelo** para gerar os resultados previstos e avaliar os modelos. O módulo **Pontuar Modelo** gera previsões para o conjunto de dados de teste usando o modelo treinado. Em seguida, transmita as pontuações ao módulo **Avaliar Modelo** para gerar métricas de avaliação.



Estes são os resultados:

![Compare os resultados](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Esses resultados mostram que o modelo criado com a **Regressão de Árvore de Decisão Aumentada** tem um erro menor de raiz quadrado da média ao modelo criado na **Regressão de Floresta de Decisão**.



## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 1: regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 3: classificação com seleção de recursos: previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5: classificação: prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 6: classificação: prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7: classificação de textos: conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)
