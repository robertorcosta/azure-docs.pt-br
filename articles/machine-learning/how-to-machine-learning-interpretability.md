---
title: Interpretável de modelo sinuoso no Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como explicar por que seu modelo faz previsões usando o Azure Machine Learning SDK. Ele pode ser usado durante o treinamento e inferência para entender como seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063994"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretável de modelo sinuoso no Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Visão geral da interpretável do modelo

A interpretabilidade é fundamental para cientistas de dados e tomadores de decisão de negócios para garantir a conformidade com as políticas da empresa, padrões do setor e regulamentos governamentais:
+ Os cientistas de dados precisam da capacidade de explicar seus modelos para executivos e partes interessadas, para que possam entender o valor e a precisão de suas descobertas 
+ Os tomadores de decisão de negócios precisam de paz de espírito da capacidade de fornecer transparência para que os usuários finais ganhem e mantenham sua confiança

Permitir a capacidade de explicar um modelo de aprendizagem de máquina é importante durante duas fases principais do desenvolvimento do modelo:
+ Durante a fase de treinamento do ciclo de desenvolvimento do modelo de aprendizagem de máquina. Designers e avaliadores de modelos podem usar a produção interpretável de um modelo para verificar hipóteses e construir confiança com as partes interessadas. Eles também usam os insights sobre o modelo para depuração, validação do comportamento do modelo corresponde aos seus objetivos e para verificar se há viés ou características insignificantes.
+ Durante a fase de inferência, como ter transparência em torno dos modelos implantados, capacita os executivos a entender "quando implantados" como o modelo está funcionando e como suas decisões estão tratando e impactando as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretável com aprendizado de máquina do Azure

Neste artigo, você aprende como os conceitos de interpretação de modelos são implementados no SDK.

Usando as classes e métodos no SDK, você pode obter:
+ Valores de importância de recursos para recursos brutos e projetados
+ Interpretabilidade em conjuntos de dados do mundo real em escala, durante o treinamento e inferência.
+ Visualizações interativas para ajudá-lo na descoberta de padrões em dados e explicações na hora do treinamento


No aprendizado de máquina, **os recursos** são os campos de dados usados para prever um ponto de dados alvo. Por exemplo, para prever o risco de crédito, campos de dados para idade, tamanho da conta e idade da conta podem ser usados. Neste caso, idade, tamanho da conta e idade da conta são **características.** A importância do recurso diz como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser muito usada na previsão, enquanto o tamanho da conta e a idade não afetam significativamente a precisão da previsão. Esse processo permite que os cientistas de dados expliquem as previsões resultantes, para que as partes interessadas tenham visibilidade sobre quais pontos de dados são mais importantes no modelo.

Usando essas ferramentas, você pode explicar modelos de aprendizado de máquina **globalmente em todos os dados**, ou **localmente em um ponto** de dados específico usando as tecnologias de última geração de forma fácil e escalável.

As classes de interpretabilidade são disponibilizadas através de vários pacotes SDK. Saiba como [instalar pacotes SDK para Aprendizado de Máquina do Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, o pacote principal, contendo funcionalidades suportadas pela Microsoft.

* `azureml.contrib.interpret`, visualização e funcionalidades experimentais que você pode tentar.

* `azureml.train.automl.automlexplainer`pacote para interpretar modelos automatizados de aprendizado de máquina.

> [!IMPORTANT]
> O conteúdo `contrib` no namespace não é totalmente suportado. À medida que as funcionalidades experimentais se amadurecem, elas serão gradualmente movidas para o espaço de nome principal.

## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Você pode aplicar as classes e métodos de interpretação para entender o comportamento global do modelo ou previsões específicas. A primeira é chamada de explicação global e a segunda é chamada de explicação local.

Os métodos também podem ser categorizados com base em se o método é agnóstico modelo ou específico do modelo. Alguns métodos têm como alvo certos tipos de modelos. Por exemplo, o explicador de árvores do SHAP só se aplica a modelos baseados em árvores. Alguns métodos tratam o modelo como uma caixa preta, como imitar explicador ou explicador de kernel shap. O `interpret` pacote aproveita essas diferentes abordagens com base em conjuntos de dados, tipos de modelos e casos de uso.

A saída é um conjunto de informações sobre como um determinado modelo faz sua previsão, tais como:
* Importância da característica relativa global/local
* Relação de recursos e previsões globais/locais

### <a name="explainers"></a>Explainers

Este pacote usa as técnicas de interpretabilidade desenvolvidas no [Interpret-Community](https://github.com/interpretml/interpret-community/), um pacote python de código aberto para treinar modelos interpretáveis e ajudar a explicar os sistemas de IA da caixa preta. [A Interpret-Community](https://github.com/interpretml/interpret-community/) serve como o host para os explicadores suportados pelo SDK, e atualmente suporta as seguintes técnicas de interpretação:

* **Shap Tree Explainer**: Explicador de árvores do [SHAP,](https://github.com/slundberg/shap)que se concentra no algoritmo de estimativa de valor rápido shap de tempo polinomial específico para árvores e conjuntos de árvores.
* **SHAP Deep Explainer**: Baseado na explicação do [SHAP](https://github.com/slundberg/shap), Deep Explainer "é um algoritmo de aproximação de alta velocidade para valores SHAP em modelos de aprendizagem profunda que se baseia em uma conexão com deeplift descrita no [papel SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Os modelos TensorFlow e Keras usando o backend TensorFlow são suportados (também há suporte preliminar para PyTorch)".
* **Shap Linear Explainer**: O explicador linear do [SHAP](https://github.com/slundberg/shap)calcula os valores shap para um modelo linear, representando opcionalmente correlações entre características.

* **Shap Kernel Explainer**: O explicador de kernel do [SHAP](https://github.com/slundberg/shap)usa uma regressão linear local especialmente ponderada para estimar os valores shap para qualquer modelo.
* **Mimic Explainer**: O explicador mimic é baseado na idéia de treinar [modelos substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos de caixa preta. Um modelo de substituto global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de um modelo de caixa preta da forma mais precisa possível. O cientista de dados pode interpretar o modelo substituto para tirar conclusões sobre o modelo da caixa preta. Você pode usar um dos seguintes modelos interpretáveis como seu modelo substituto: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Modelo explicável de Descida de Gradiente Estocástico (SGDExplainableModel) e Árvore de Decisão ( DecisionTreeExplainableModel).


* **Permutation Feature Importance Explainer**: Permutation Feature Importance é uma técnica usada para explicar modelos de classificação e regressão que é inspirado no [artigo Random Forests de Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (ver seção 10). Em um alto nível, a maneira como ele funciona é embaralhando aleatoriamente dados um recurso de cada vez para todo o conjunto de dados e calculando o quanto a métrica de desempenho de interesse muda. Quanto maior a alteração, mais importante é esse recurso.

* **Lime Explainer** (`contrib`): Com base em [LIME](https://github.com/marcotcr/lime), LIME Explainer usa o algoritmo local interpretável de explicações agnósticas (LIME) para criar modelos substitutos locais. Ao contrário dos modelos de substitutos globais, a LIME se concentra em treinar modelos substitutos locais para explicar previsões individuais.
* **Han Text Explainer** (`contrib`): HAN Text Explainer usa uma Rede de Atenção Hierárquica para obter explicações de modelo a partir de dados de texto para um determinado modelo de texto caixa preta. Ele treina o modelo de substituto HAN em uma determinada caixa preta de saídas previstas. Depois de treinar globalmente através do corpus de texto, ele adiciona um passo de ajuste fino para um documento específico, a fim de melhorar a precisão das explicações. HAN usa um RNN bidirecional com duas camadas de atenção, para a sentença e atenção à palavra. Uma vez que o DNN é treinado no modelo de caixa preta e ajustado em um documento específico, o usuário pode extrair as importâncias da palavra das camadas de atenção. O HAN mostra-se mais preciso do que o LIME ou o SHAP para dados de texto, mas mais caro em termos de tempo de treinamento também. Melhorias foram feitas para dar ao usuário a opção de inicializar a rede com incorporações de palavras GloVe para reduzir o tempo de treinamento. O tempo de treinamento pode ser melhorado significativamente executando HAN em uma VM de GPU Azure remota. A implementação do HAN é descrita em 'Redes de [Atenção Hierárquica para Classificação de Documentos (Yang et al., 2016)'](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Explicador Tabular**: `TabularExplainer` emprega a seguinte lógica para invocar os Explicadores [Direct SHAP:](https://github.com/slundberg/shap)

    1. Se for um modelo baseado em `TreeExplainer`árvores, aplique SHAP , caso mais
    2. Se for um modelo DNN, `DeepExplainer`aplique SHAP , caso mais
    3. Se for um modelo linear, `LinearExplainer`aplique SHAP , caso mais
    3. Trate-o como um modelo de caixa preta e aplique SHAP`KernelExplainer`


`TabularExplainer`também fez melhorias significativas de recursos e desempenho sobre os explicadores shap diretos:

* **Somada do conjunto de dados de inicialização**. Nos casos em que a velocidade de explicação é mais importante, resumimos o conjunto de dados de inicialização e geramos um pequeno conjunto de amostras representativas, o que acelera tanto a explicação global quanto a local.
* **Amostragem do conjunto de dados de avaliação**. Se o usuário passar em um grande conjunto de amostras de avaliação, mas não precisar de todas elas para ser avaliada, o parâmetro de amostragem pode ser definido como verdadeiro para acelerar a explicação global.

O diagrama a seguir mostra a estrutura atual dos explicadores diretos e meta.

[![Arquitetura de interpretabilidade de aprendizado de máquina](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelos suportados

Todos os modelos treinados `numpy.array`em `pandas.DataFrame` `iml.datatypes.DenseData`conjuntos `scipy.sparse.csr_matrix` de dados em Python, ou formato são suportados pelo pacote de interpretabilidade `explain` do SDK.

As funções de explicação aceitam modelos e gasodutos como entrada. Se um modelo for fornecido, o modelo `predict` `predict_proba` deve implementar a função de previsão ou que esteja em conformidade com a convenção scikit. Se um pipeline (nome do script de pipeline) for fornecido, a função de explicação assume que o script de pipeline em execução retorna uma previsão. Apoiamos modelos treinados via PyTorch, TensorFlow e Keras frameworks de aprendizagem profunda.

### <a name="local-and-remote-compute-target"></a>Alvo de computação local e remota

O `explain` pacote foi projetado para funcionar com alvos de computação local e remoto. Se for executado localmente, as funções do SDK não entrarão em contato com nenhum serviço do Azure. Você pode executar a explicação remotamente no Azure Machine Learning Compute e registrar as informações de explicação no Azure Machine Learning Run History Services. Uma vez registradas essas informações, relatórios e visualizações da explicação estão prontamente disponíveis no espaço de trabalho azure Machine Learning para análise do usuário.


## <a name="next-steps"></a>Próximas etapas

Veja [o como fazer](how-to-machine-learning-interpretability-aml.md) para habilitar a interpretável para modelos que treinam tanto localmente quanto em recursos de computação remota do Azure Machine Learning. Consulte os [cadernos de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para obter cenários adicionais.
