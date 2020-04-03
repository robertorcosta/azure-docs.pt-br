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
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389334"
---
# <a name="designer-sample-pipelines"></a>Pipelines de exemplo do designer

Use os exemplos internos do designer do Azure Machine Learning para começar rapidamente a criar os próprios pipelines de aprendizado de máquina. O [repositório GitHub](https://github.com/Azure/MachineLearningDesigner) do designer do Azure Machine Learning contém uma documentação detalhada para ajudar você a entender alguns cenários comuns de aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).
* Um workspace do Azure Machine Learning com o SKU Enterprise.


## <a name="how-to-use-sample-pipelines"></a>Como usar pipelines de exemplo

O designer salva uma cópia dos pipelines de exemplo no seu workspace do estúdio. Você pode editar o pipeline para adaptá-lo às suas necessidades e salvá-lo como o próprio. Use-os como um ponto de partida para iniciar rapidamente seus projetos.

### <a name="open-a-sample-pipeline"></a>Abrir um pipeline de exemplo

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Designer**.

1. Selecione um pipeline de exemplo na seção **Novo pipeline**.

    Selecione **Mostrar mais amostras** para obter uma lista completa de amostras.

### <a name="submit-a-pipeline-run"></a>Enviar uma execução de pipeline

Para executar um pipeline, primeiro defina o destino de computação padrão no qual executar o pipeline.

1. No painel **Configurações** à direita da tela, selecione **Selecionar de destino de computação**.

1. Na caixa de diálogo exibida, selecione um destino de computação existente ou crie um. Clique em **Salvar**.

1. Selecione **Enviar** na parte superior da tela para enviar uma execução de pipeline.

Dependendo do pipeline de exemplo e das configurações de computação, as execuções podem levar algum tempo para serem concluídas. As configurações de computação padrão têm um tamanho de nó mínimo de 0, o que significa que o designer precisa alocar recursos depois de ficar ocioso. Execuções de pipeline repetidas levarão menos tempo, já que os recursos de computação já estão alocados. Além disso, o designer usa resultados armazenados em cache para cada módulo para melhorar ainda mais a eficiência.


### <a name="review-the-results"></a>Revise os resultados

Depois que a execução do pipeline for concluída, você poderá examinar o pipeline e exibir a saída de cada módulo para saber mais.

Use as seguintes etapas para exibir as saídas do módulo:

1. Selecione um módulo na tela.

1. No painel de detalhes do módulo à direita da tela, selecione **Saídas + logs**. Selecione o ícone de grafo ![ícone de visualizar](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para ver os resultados de cada módulo. 

Use os exemplos como pontos de partida para alguns dos cenários de aprendizado de máquina mais comuns.

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
