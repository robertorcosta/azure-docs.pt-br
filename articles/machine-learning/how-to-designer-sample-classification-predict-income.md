---
title: 'Designer: exemplo de classificação e previsão de receita'
titleSuffix: Azure Machine Learning
description: Siga este exemplo para criar um classificador sem código e prever a receita com o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 560339fb04e3bbbe42c4370655e74e8536a7c015
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963353"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Crie um classificador e use a seleção de recursos para prever a receita com o designer do Azure Machine Learning

**Amostra 3 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um classificador de aprendizado de máquina sem escrever uma só linha de código usando o designer (versão prévia). Esta amostra treina uma **árvore de decisão aumentada de duas classes** para prever a receita de censo de adultos (> = 50 mil ou < = 50 mil).

Como a pergunta responde a "Qual deles?", isso é chamado de problema de classificação. No entanto, você pode aplicar o mesmo processo fundamental para resolver qualquer tipo de problema de aprendizado de máquina: regressão, classificação, clustering etc.

Este é o grafo do pipeline final desta amostra:

![Grafo do pipeline](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 3 para abri-la.



## <a name="data"></a>Dados

O conjunto de dados contém 14 recursos e uma coluna de rótulo. Há vários tipos de recursos, incluindo numéricos e categóricos. O seguinte diagrama mostra um trecho do conjunto de dados: ![dados](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Resumo do pipeline

Siga estas etapas para criar o pipeline:

1. Arraste o módulo do conjunto de dados Binário de Receita de Censo de Adultos para a tela do pipeline.
1. Adicione um módulo **Dividir Dados** para criar os conjuntos de treinamento e teste. Defina a fração de linhas no primeiro conjunto de dados de saída como 0,7. Essa configuração especifica que 70% dos dados serão gerados na porta esquerda do módulo e o restante na porta direita. Usamos o conjunto de dados da esquerda para treinamento e o da direita para teste.
1. Adicione o módulo **Seleção de Recursos Baseada em Filtro** para selecionar cinco recursos por PearsonCorreclation. 
1. Adicione um módulo **Árvore de Decisão Aumentada de Duas Classes** para inicializar um classificador de árvore de decisão aumentada.
1. Adicione um módulo **Treinar Modelo**. Conecte o classificador da etapa anterior à porta de entrada esquerda do módulo **Treinar Modelo**. Conecte o conjunto de dados filtrado do módulo Seleção de Recursos Baseada em Filtro como o conjunto de dados de treinamento.  O módulo **Treinar Modelo** treinará o classificador.
1. Adicione o módulo Selecionar Transformação de Colunas e Aplicar Transformação para aplicar a mesma transformação (seleção de recursos baseada em filtro) para testar o conjunto de dados.
![apply-transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Adicione o módulo **Pontuar Modelo** e conecte o módulo **Treinar Modelo** a ele. Em seguida, adicione o conjunto de teste (a saída do módulo Aplicar Transformação que aplica a seleção de recursos ao conjunto de teste também) ao módulo **Pontuar Modelo**. O módulo **Pontuar Modelo** fará as previsões. Selecione a porta de saída para ver as previsões e as probabilidades de classe positiva.


    Este pipeline tem dois módulos de pontuação; aquele à direita excluiu a coluna de rótulo antes de fazer a previsão. Isso está preparado para implantar um ponto de extremidade em tempo real, pois a entrada do serviço Web vai esperar apenas recursos, não o rótulo. 

1. Adicione um módulo **Avaliar Modelo** e conecte o conjunto de dados pontuado à porta de entrada esquerda. Para ver os resultados da avaliação, selecione a porta de saída do módulo **Avaliar Modelo** e selecione **Visualizar**.

## <a name="results"></a>Resultados

![Avaliar os resultados](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

Nos resultados da avaliação, você poderá ver as curvas, como ROC, precisão/recall e métricas de confusão. 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 1: regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2: regressão: comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5: classificação: prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 6: classificação: prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7: classificação de textos: conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)
