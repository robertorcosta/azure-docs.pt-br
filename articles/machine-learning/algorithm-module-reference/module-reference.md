---
title: Referência de módulo e algoritmo
description: Conheça os módulos disponíveis no azure Machine Learning designer (visualização)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: c24c7ce1d47a4734d79d7e17e9fb4b3a2d4d70e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064152"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritmo & referência de módulo para azure Machine Learning designer (visualização)

Este conteúdo de referência fornece o conhecimento técnico de cada um dos algoritmos e módulos de aprendizado de máquina disponíveis no azure Machine Learning designer (visualização).

Cada módulo representa um conjunto de códigos que podem ser executados independentemente e executar uma tarefa de aprendizado de máquina, dadas as entradas necessárias. Um módulo pode conter um algoritmo específico ou executar uma tarefa que seja importante no aprendizado de máquina, tal como substituição de um valor ausente ou análise estatística.

Para obter ajuda na escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Folha de trapaça do algoritmo de aprendizagem de máquina do Azure](../../synapse-analytics/sql-data-warehouse/cheat-sheet.md)

> [!TIP]
> Em qualquer pipeline do designer, você pode obter informações sobre um módulo específico. Selecione o módulo e, em seguida, selecione o link **mais ajuda** no painel **Ajuda Rápida**.

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Entrada e saída de dados | Mova dados de fontes de nuvem para o seu pipeline. Escreva seus resultados ou dados intermediários para o Azure Storage, um banco de dados SQL ou Hive, enquanto executa um pipeline ou use armazenamento em nuvem para trocar dados entre pipelines.  | [Inserir Dados Manualmente](enter-data-manually.md) <br/> [Exportar dados](export-data.md) <br/> [Importar dados](import-data.md) |
| Transformação de dados | Operações em dados exclusivos para aprendizado de máquina, como normalização ou binning de dados, redução da dimensionalidade e conversão de dados entre vários formatos de arquivos.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar linhas](add-rows.md) <br/> [Aplicar operação matemática](apply-math-operation.md) <br/> [Aplicar transformação de SQL](apply-sql-transformation.md) <br/> [Limpar dados ausentes](clean-missing-data.md) <br/> [Recortar valores](clip-values.md) <br/> [Conversão em CSV](convert-to-csv.md) <br/> [Converter em conjunto de dados](convert-to-dataset.md) <br/> [Converter em valores de indicador](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Unir dados](join-data.md) <br/> [Normalizar Dados](normalize-data.md) <br/> [Partição e Amostra](partition-and-sample.md)  <br/> [Remover Linhas Duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar colunas para transformação](select-columns-transform.md) <br/> [Projetar Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dividir Dados](split-data.md) |
| Seleção de recursos | Selecione um subconjunto de recursos úteis e relevantes para usar na construção de um modelo analítico. | [Seleção de recursos baseada em filtro](filter-based-feature-selection.md) <br/> [Importância do recurso de permuta](permutation-feature-importance.md) |
| Funções estatísticas | Fornecer uma grande variedade de métodos estatísticos relacionados à ciência de dados. | [Resumir dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizado de máquina

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão de Árvore de Decisão Aumentada](boosted-decision-tree-regression.md) <br/> [Regressão de Floresta de Decisão](decision-forest-regression.md) <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão de Rede Neural](neural-network-regression.md)  <br/> |
| Clustering | Dados de grupo juntos.| [Cluster K-means](k-means-clustering.md)
| classificação | Prever uma aula.  Escolha entre algoritmos binários (de duas classes) ou multiclasse.| [Árvore de Decisão Aumentada Multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão Multiclasse](multiclass-decision-forest.md) <br/> [Regressão Logística Multiclasse](multiclass-logistic-regression.md)  <br/> [Rede Neural Multiclasse](multiclass-neural-network.md) <br/> [Um vs. Todas multiclasse](one-vs-all-multiclass.md) <br/> [Perceptron Médio de Duas Classes](two-class-averaged-perceptron.md) <br/>  [Árvore de Decisão Aumentada de Duas Classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de Decisão de Duas Classes](two-class-decision-forest.md) <br/>  [Regressão Logística de Duas Classes](two-class-logistic-regression.md) <br/> [Rede Neural de Duas Classes](two-class-neural-network.md) <br/> [Computador de vetor de suporte de duas classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para construção e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Treinamento do modelo | Execute dados através do algoritmo. |  [Treinar Modelo de Clustering](train-clustering-model.md) <br/> [Treinar Modelo](train-model.md)  <br/> [Ajustar os hiperparâmetros de modelo](tune-model-hyperparameters.md) |
| Pontuação e Avaliação de Modelos | Meça a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Atribuir dados a clusters](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Avaliar Modelo](evaluate-model.md) <br/> [Pontuar Modelo](score-model.md) |
| Linguagem Python | Escreva código e incorpore-o em um módulo para integrar Python com seu pipeline. | [Criar Modelo do Python](create-python-model.md) <br/> [Executar Script do Python](execute-python-script.md) |
| Linguagem R | Escreva código e incorpore-o em um módulo para integrar R com seu pipeline. | [Executar script R](execute-r-script.md) |
| Análise de texto | Fornecer ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. | [Extrair recursos N-Gram do texto](extract-n-gram-features-from-text.md) <br/> [Hash de recursos](feature-hashing.md) <br/> [Pré-processar Texto](preprocess-text.md) <br/> [Alocação latente de Dirichlet](latent-dirichlet-allocation.md) |
| Recomendação | Construa modelos de recomendação. | [Avaliar o sistema de recomendação](evaluate-recommender.md) <br/> [Pontuar o sistema de recomendação SVD](score-svd-recommender.md) <br/> [Treinar o sistema de recomendação SVD](train-SVD-recommender.md) |
| Detecção de anomalias | Construa modelos de detecção de anomalias. | [Detecção de anomalia baseada em PCA](pca-based-anomaly-detection.md) <br/> [Modelo de detecção de anomalias de treinamento](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as [mensagens de erro e códigos de exceção](designer-error-codes.md) que você pode encontrar usando módulos no designer de Machine Learning do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Construa um modelo em designer para prever preços de automóveis](../tutorial-designer-automobile-price-train-score.md)
