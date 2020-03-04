---
title: 'Designer: exemplo de previsão de rotatividade'
titleSuffix: Azure Machine Learning
description: Siga este exemplo de classificação para prever a rotatividade com o designer do Azure Machine Learning e árvores de decisão aumentadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 701bf186080ef627ef4621e959b281fd58aa4132
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963350"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Use a árvore de decisão aumentada para prever a rotatividade com o designer do Azure Machine Learning

**Amostra 5 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um pipeline complexo de aprendizado de máquina sem escrever uma só linha de código usando o designer (versão prévia).

Este pipeline treina dois classificadores de **árvore de decisão aumentada de duas classes** para prever tarefas comuns para sistemas de CRM (gerenciamento de relacionamento com o cliente)/rotatividade de clientes. Os rótulos e os valores de dados são divididos em várias fontes de dados e embaralhados para tornar as informações do cliente anônimas; no entanto, ainda podemos usar o designer para combinar conjuntos de dados e treinar um modelo usando os valores obscurecidos.

Como você está tentando responder à pergunta "Qual deles?", isso é chamado de problema de classificação, mas você pode aplicar a mesma lógica apresentada nesta amostra para resolver qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering etc.

Este é o grafo completo deste pipeline:

![Grafo do pipeline](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 5 para abri-la. 

## <a name="data"></a>Dados

Os dados desse pipeline pertencem ao KDD Cup 2009. Ele tem 50.000 linhas e 230 colunas de recursos. A tarefa é prever a rotatividade, a inclinação e a venda adicional para os clientes que usam esses recursos. Para obter mais informações sobre os dados e a tarefa, confira o site do [KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Resumo do pipeline

Este pipeline de exemplo no designer mostra a previsão do classificador binário de rotatividade, inclinação e venda adicional, uma tarefa comum no CRM (gerenciamento de relacionamento com o cliente).

Primeiro, um processamento de dados simples.

- O conjunto de dados brutos tem muitos valores ausentes. Use o módulo **Limpar Dados Ausentes** para substituir os valores ausentes por 0.

    ![Limpar o conjunto de dados](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Os recursos e a rotatividade correspondente estão em conjuntos de dados diferentes. Use o módulo **Adicionar Colunas** para acrescentar as colunas de rótulo às colunas de recurso. A primeira coluna, **Col1**, é a coluna de rótulo. Com base no resultado da visualização, podemos ver que o conjunto de dados está desequilibrado. Há muito mais exemplos negativos (-1) do que exemplos positivos (+1). Usaremos o módulo **SMOTE** para aumentar os casos sub-representados mais tarde.

    ![Adicionar o conjunto de dados de colunas](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Use o módulo **Dividir Dados** para dividir o conjunto de dados em conjuntos de treinamento e teste.

- Em seguida, use o classificador binário da Árvore de Decisão Aumentada com os parâmetros padrão para criar os modelos de previsão. Crie um modelo por tarefa, ou seja, um modelo para prever a venda adicional, a inclinação e a rotatividade.

- Na parte direita do pipeline, usamos o módulo **SMOTE** para aumentar o percentual de exemplos positivos. O percentual de SMOTE é definido como 100 para dobrar os exemplos positivos. Saiba mais sobre como o módulo SMOTE funciona com a [referência do módulo SMOTE](algorithm-module-reference/smote.md).

## <a name="results"></a>Resultados

Visualize a saída do módulo **Avaliar Modelo** para ver o desempenho do modelo no conjunto de teste. 

![Avaliar os resultados](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 Você pode mover o controle deslizante **Limite** e ver as métricas alteradas na tarefa de classificação binária. 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 1 – Regressão: Prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2 – Regressão: Comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3 – Classificação com seleção de recursos: Previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 6 – Classificação: Prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7 – Classificação de textos: Conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)
