---
title: Referência de módulo e algoritmo
description: Saiba mais sobre os módulos disponíveis no designer de Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: c46c21b5662b924552f850f226a12c7350b4625a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458191"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Referência de módulo de & de algoritmo para o designer de Azure Machine Learning (versão prévia)

Esse conteúdo de referência fornece a experiência técnica em cada um dos módulos e algoritmos de aprendizado de máquina disponíveis no designer de Azure Machine Learning (versão prévia).

Cada módulo representa um conjunto de códigos que podem ser executados de forma independente e executar uma tarefa de aprendizado de máquina, dadas as entradas necessárias. Um módulo pode conter um algoritmo específico ou executar uma tarefa que seja importante no aprendizado de máquina, tal como substituição de um valor ausente ou análise estatística.

Para obter ajuda com a escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Roteiro de Azure Machine Learning de algoritmos](../algorithm-cheat-sheet.md)

> [!TIP]
> Em qualquer pipeline no designer, você pode obter informações sobre um módulo específico. Selecione o módulo e, em seguida, selecione o link **mais ajuda** no painel **Ajuda Rápida**.

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Entrada e saída de dados | Mova dados de fontes de nuvem para seu pipeline. Grave os resultados ou os dados intermediários no armazenamento do Azure, em um banco de dados SQL ou Hive, durante a execução de um pipeline, ou use o armazenamento em nuvem do para o Exchange data entre pipelines.  | [Inserir Dados Manualmente](enter-data-manually.md) <br/> [Exportar dados](export-data.md) <br/> [Importar dados](import-data.md) |
| Transformação de dados | Operações em dados que são exclusivas do aprendizado de máquina, como normalização ou compartimentalização de dados, redução de dimensionalidade e conversão de dados entre vários formatos de arquivo.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar linhas](add-rows.md) <br/> [Aplicar operação matemática](apply-math-operation.md) <br/> [Aplicar transformação de SQL](apply-sql-transformation.md) <br/> [Limpar dados ausentes](clean-missing-data.md) <br/> [Recortar valores](clip-values.md) <br/> [Conversão em CSV](convert-to-csv.md) <br/> [Converter em conjunto de dados](convert-to-dataset.md) <br/> [Converter em valores de indicador](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Unir dados](join-data.md) <br/> [Normalizar Dados](normalize-data.md) <br/> [Particionar e Gerar Amostra](partition-and-sample.md)  <br/> [Remover Linhas Duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar colunas para transformação](select-columns-transform.md) <br/> [Projetar Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dividir Dados](split-data.md) |
| Seleção de recursos | Selecione um subconjunto de recursos relevantes e úteis a serem usados na criação de um modelo analítico. | [Seleção de recursos baseada em filtro](filter-based-feature-selection.md) <br/> [Importância do recurso de permuta](permutation-feature-importance.md) |
| Funções estatísticas | Forneça uma ampla variedade de métodos estatísticos relacionados à ciência de dados. | [Resumir dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizado de máquina

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão de Árvore de Decisão Aumentada](boosted-decision-tree-regression.md) <br/> [Regressão de Floresta de Decisão](decision-forest-regression.md) <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão de Rede Neural](neural-network-regression.md)  <br/> |
| Clustering | Agrupar dados juntos.| [Cluster K-means](k-means-clustering.md)
| classificação | Prever uma classe.  Escolha entre os algoritmos binário (duas classes) ou multiclasse.| [Árvore de Decisão Aumentada Multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão Multiclasse](multiclass-decision-forest.md) <br/> [Regressão Logística Multiclasse](multiclass-logistic-regression.md)  <br/> [Rede Neural Multiclasse](multiclass-neural-network.md) <br/> [Multiclasse Um contra Todos](one-vs-all-multiclass.md) <br/> [Perceptron Médio de Duas Classes](two-class-averaged-perceptron.md) <br/>  [Árvore de decisão aumentada em duas classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de Decisão de Duas Classes](two-class-decision-forest.md) <br/>  [Regressão Logística de Duas Classes](two-class-logistic-regression.md) <br/> [Rede Neural de Duas Classes](two-class-neural-network.md) <br/> [Computador de vetor de suporte de duas classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para criação e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Treinamento do modelo | Execute os dados por meio do algoritmo. |  [Treinar Modelo de Clustering](train-clustering-model.md) <br/> [Treinar Modelo](train-model.md)  <br/> [Ajustar os hiperparâmetros de modelo](tune-model-hyperparameters.md) |
| Pontuação e avaliação do modelo | Meça a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Atribuir dados a clusters](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Avaliar Modelo](evaluate-model.md) <br/> [Pontuar Modelo](score-model.md) |
| Linguagem Python | Escreva o código e incorpore-o em um módulo para integrar o Python ao seu pipeline. | [Criar Modelo do Python](create-python-model.md) <br/> [Executar Script do Python](execute-python-script.md) |
| Linguagem R | Escreva o código e incorpore-o em um módulo para integrar o R ao seu pipeline. | [Executar script R](execute-r-script.md) |
| Análise de texto | Forneça ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. | [Extrair recursos N-Gram do texto](extract-n-gram-features-from-text.md) <br/> [Hash de recursos](feature-hashing.md) <br/> [Pré-processar Texto](preprocess-text.md) <br/> [Alocação de Dirichlet latente](latent-dirichlet-allocation.md) |
| Recomendação | Crie modelos de recomendação. | [Avaliar o sistema de recomendação](evaluate-recommender.md) <br/> [Pontuar o sistema de recomendação SVD](score-svd-recommender.md) <br/> [Treinar o sistema de recomendação SVD](train-SVD-recommender.md) |
| Detecção de anomalias | Crie modelos de detecção de anomalias. | [Detecção de anomalias baseada em PCA](pca-based-anomaly-detection.md) <br/> [Treinar um modelo de detecção de anomalias](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Serviço Web

Saiba mais sobre os [módulos de serviço Web](web-service-input-output.md) que são necessários para a inferência em tempo real no designer de Azure Machine Learning.

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as [mensagens de erro e os códigos de exceção](designer-error-codes.md) que você pode encontrar usando módulos no designer de Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: criar um modelo no designer para prever preços automáticos](../tutorial-designer-automobile-price-train-score.md)
