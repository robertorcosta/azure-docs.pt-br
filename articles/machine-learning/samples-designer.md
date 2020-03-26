---
title: Pipelines de exemplo do designer
titleSuffix: Azure Machine Learning
description: Use amostras no designer do Azure Machine Learning para iniciar rapidamente seus pipelines de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/10/2020
ms.openlocfilehash: 82476b9cc8d92c815df602496ed3dcb33014a4fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037291"
---
# <a name="designer-sample-pipelines"></a>Pipelines de exemplo do designer

Use os exemplos internos do designer do Azure Machine Learning para começar rapidamente a criar os próprios pipelines de aprendizado de máquina. O [repositório GitHub](https://github.com/Azure/MachineLearningDesigner) do designer do Azure Machine Learning contém uma documentação detalhada para ajudar você a entender alguns cenários comuns de aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).
* Um workspace do Azure Machine Learning com o SKU Enterprise.


## <a name="how-to-use-sample-pipelines"></a>Como usar pipelines de exemplo

O designer salva uma cópia dos pipelines de exemplo no seu workspace do estúdio. Você pode editar o pipeline para adaptá-lo às suas necessidades e salvá-lo como o próprio. Use-os como um ponto de partida para iniciar rapidamente seus projetos.

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Designer**.

1. Selecione um pipeline de exemplo na seção **Novo pipeline**.

    Selecione **Mostrar mais amostras** para obter uma lista completa de amostras.

## <a name="regression-samples"></a>Amostras de regressão

Saiba mais sobre as amostras de regressão internas.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 1: regressão – previsão de preços de automóveis (básica)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Preveja os preços de carros usando a regressão linear. |
| [Amostra 2: regressão – previsão de preço de automóveis (avançada)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Preveja os preços de carros usando a floresta de decisão e os regressores de árvore de decisão aumentada. Compare os modelos para encontrar o melhor algoritmo.

## <a name="classification-samples"></a>Amostras de classificação

Saiba mais sobre as amostras de classificação internas. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 3: classificação binária com seleção de recursos – previsão de receita](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Preveja a receita como alta ou baixa usando uma árvore de decisão aumentada de duas classes. Use a correlação de Pearson para selecionar recursos.
| [Amostra 4: classificação binária com script Python personalizado – previsão de risco de crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classifique os pedidos de crédito como de alto ou baixo risco. Use o módulo Executar Script Python para ponderar seus dados.
| [Amostra 5: classificação binária – previsão de relacionamento com o cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Preveja a rotatividade de clientes usando árvores de decisão aumentadas de duas classes. Use o SMOTE para fazer a amostragem dos dados polarizados.
| [Amostra 7: classificação de textos – conjunto de dados SP 500 da Wikipédia](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classifique os tipos de empresas com base nos artigos da Wikipédia com a regressão logística multiclasse. |
| Amostra 12: Classificação multiclasse – reconhecimento de letras | Crie um conjunto de classificadores binários para classificar letras escritas. |

## <a name="recommender-samples"></a>Amostras de recomendação

Saiba mais sobre as amostras de recomendação internas. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| Amostra 10: recomendação – tweets de classificação de filmes | Crie um mecanismo de recomendação de filmes com base nos títulos e na classificação de filmes. |

## <a name="utility-samples"></a>Amostras de utilitário

Saiba mais sobre as amostras que descrevem os recursos e os utilitários de aprendizado de máquina. Saiba mais sobre as amostras sem os links de documentação abrindo as amostras e vendo os comentários do módulo.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Amostra 6: usar um script R personalizado – previsão de atraso de voos](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Amostra 8: validação cruzada para classificação binária – previsão de receita de adulto | Use a validação cruzada para criar um classificador binário para a receita de adulto.
| Amostra 9: Importância do recurso de permuta | Use a importância do recurso de permuta para computar pontuações de importância para o conjunto de dados de teste. 
| Amostra 11: ajustar parâmetros para classificação binária – previsão de receita de adulto | Use os hiperparâmetros do modelo de ajuste para encontrar hiperparâmetros ideais para a criação de um classificador binário. |

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba como criar e implantar modelos de machine learning com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
