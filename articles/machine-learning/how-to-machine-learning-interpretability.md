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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631410"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretável de modelo sinuoso no Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Visão geral da interpretável do modelo

A interpretabilidade é fundamental para cientistas de dados, auditores e tomadores de decisão de negócios para garantir o cumprimento das políticas da empresa, padrões do setor e regulamentos governamentais:

+ Os cientistas de dados precisam da capacidade de explicar seus modelos aos executivos e stakeholders, para que possam entender o valor e a precisão de suas descobertas. Eles também exigem a interpretabilidade para depurar seus modelos e tomar decisões informadas sobre como melhorá-los. 

+ Os auditores legais exigem ferramentas para validar modelos em relação à conformidade normativa e monitorar como as decisões dos modelos estão afetando os seres humanos. 

+ Os tomadores de decisão de negócios precisam de paz de espírito, tendo a capacidade de fornecer transparência para os usuários finais. Isso permite que eles ganhem e mantenham a confiança.


Permitir a capacidade de explicar um modelo de aprendizagem de máquina é importante durante duas fases principais do desenvolvimento do modelo:
+ Durante a fase de treinamento, como designers de modelos e avaliadores podem usar a produção interpretável de um modelo para verificar hipóteses e construir confiança com as partes interessadas. Eles também usam os insights sobre o modelo para depuração, validação do comportamento do modelo corresponde aos seus objetivos e para verificar se há injustiça no modelo ou características insignificantes.

+ Durante a fase de inferência, como ter transparência em torno dos modelos implantados, capacita os executivos a entender "quando implantados" como o modelo está funcionando e como suas decisões estão tratando e impactando as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretável com aprendizado de máquina do Azure

As aulas de interpretação são disponibilizadas através de vários pacotes SDK: (Saiba como [instalar pacotes SDK para Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, o pacote principal, contendo funcionalidades suportadas pela Microsoft.

* `azureml.contrib.interpret`, visualização e funcionalidades experimentais que você pode tentar.

* `azureml.train.automl.automlexplainer`pacote para interpretar modelos automatizados de aprendizado de máquina.

Use `pip install azureml-interpret` `pip install azureml-interpret-contrib` e para uso `pip install azureml-interpret-contrib` geral, e para o uso do AutoML para obter os pacotes de interpretabilidade.


> [!IMPORTANT]
> O conteúdo `contrib` no namespace não é totalmente suportado. À medida que as funcionalidades experimentais se amadurecem, elas serão gradualmente movidas para o espaço de nome principal.
.



## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Usando as classes e métodos no SDK, você pode:
+ Explique a previsão do modelo gerando valores de importância de características para todo o modelo e/ou pontos de dados individuais. 
+ Alcançar a interpretabilidade do modelo em conjuntos de dados do mundo real em escala, durante o treinamento e inferência.
+ Use um painel de visualização interativo para descobrir padrões em dados e explicações na hora do treinamento


No aprendizado de máquina, **os recursos** são os campos de dados usados para prever um ponto de dados alvo. Por exemplo, para prever o risco de crédito, campos de dados para idade, tamanho da conta e idade da conta podem ser usados. Neste caso, idade, tamanho da conta e idade da conta são **características.** A importância do recurso diz como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser muito usada na previsão, enquanto o tamanho da conta e a idade não afetam significativamente os valores de previsão. Esse processo permite que os cientistas de dados expliquem as previsões resultantes, para que as partes interessadas tenham visibilidade sobre quais características são mais importantes no modelo.

Aprenda sobre técnicas de interpretabilidade suportadas, modelos de aprendizado de máquina suportados e ambientes de execução suportados aqui.


## <a name="supported-interpretability-techniques"></a>Técnicas de interpretação suportadas

 `azureml-interpret`usa as técnicas de interpretabilidade desenvolvidas no [Interpret-Community](https://github.com/interpretml/interpret-community/), um pacote python de código aberto para treinar modelos interpretáveis e ajudar a explicar os sistemas de IA da caixa preta. [A Interpret-Community](https://github.com/interpretml/interpret-community/) serve como o host para os explicadores suportados pelo SDK, e atualmente suporta as seguintes técnicas de interpretação:

|Técnica de interpretabilidade|Descrição|Type|
|--|--|--------------------|
|1. Explicador de árvores SHAP| O explicador de árvores do [SHAP,](https://github.com/slundberg/shap)que se concentra no algoritmo de estimativa de valor rápido shap de tempo polinomial específico para **árvores e conjuntos de árvores**.|Específico do modelo|
|2. Explicador Profundo SHAP| Com base na explicação do [SHAP](https://github.com/slundberg/shap), Deep Explainer "é um algoritmo de aproximação de alta velocidade para valores SHAP em modelos de aprendizagem profunda que se baseia em uma conexão com deeplift descrita no [papel SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Os modelos **TensorFlow** e **Keras** usando o backend TensorFlow são suportados (também há suporte preliminar para PyTorch)".|Específico do modelo|
|3. Explicador Linear SHAP| O explicador linear do [SHAP](https://github.com/slundberg/shap)calcula os valores shap para um **modelo linear,** opcionalmente contabilizando correlações entre características.|Específico do modelo|
|4. Explicador de kernel SHAP| O explicador kernel do [SHAP](https://github.com/slundberg/shap)usa uma regressão linear local especialmente ponderada para estimar os valores shap para **qualquer modelo**.|Modelo-agnóstico|
|5. Explicar o Mímico (Substituto Global)| O explicador mimic é baseado na idéia de treinar [modelos substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos de caixa preta. Um modelo de substituto global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de qualquer modelo de **caixa preta** da forma mais precisa possível. Os cientistas de dados podem interpretar o modelo substituto para tirar conclusões sobre o modelo da caixa preta. Você pode usar um dos seguintes modelos interpretáveis como seu modelo substituto: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Modelo explicável de Descida de Gradiente Estocástico (SGDExplainableModel) e Árvore de Decisão (DecisionTreeExplainableModel).|Modelo-agnóstico|
|6. Explicador de importância do recurso de permutação (PFI)| Recurso de permutação A importância é uma técnica usada para explicar modelos de classificação e regressão que é inspirada no [artigo Random Forests de Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (ver seção 10). Em um alto nível, a maneira como ele funciona é embaralhando aleatoriamente dados um recurso de cada vez para todo o conjunto de dados e calculando o quanto a métrica de desempenho de interesse muda. Quanto maior a alteração, mais importante é esse recurso. O PFI pode explicar o comportamento geral de **qualquer modelo subjacente,** mas não explica previsões individuais. |Modelo-agnóstico|




Além das técnicas de interpretação descritas acima, apoiamos `TabularExplainer`outro [explicador baseado em SHAP,](https://github.com/slundberg/shap)chamado . Dependendo do modelo, `TabularExplainer` usa um dos explicadores SHAP suportados:

* TreeExplainer para todos os modelos baseados em árvores
* DeepExplainer para modelos DNN
* Explicador Linear para modelos lineares
* KernelExplainer para todos os outros modelos

`TabularExplainer`também fez melhorias significativas de recursos e desempenho sobre os explicadores shap diretos:

* **Somada do conjunto de dados de inicialização**. Nos casos em que a velocidade de explicação é mais importante, resumimos o conjunto de dados de inicialização e geramos um pequeno conjunto de amostras representativas, o que acelera a geração de valores de importância de características individuais e gerais.
* **Amostragem do conjunto de dados de avaliação**. Se o usuário passar em um grande conjunto de amostras de avaliação, mas não precisar de todas elas para ser avaliada, o parâmetro amostral pode ser definido como verdadeiro para acelerar o cálculo das explicações gerais do modelo.

O diagrama a seguir mostra a estrutura atual dos explicadores suportados.

[![Arquitetura de interpretabilidade de aprendizado de máquina](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelos de aprendizado de máquina suportados

O `azureml.interpret` pacote do SDK suporta modelos treinados com os seguintes formatos de conjunto de dados:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

As funções de explicação aceitam modelos e gasodutos como entrada. Se um modelo for fornecido, o modelo `predict` `predict_proba` deve implementar a função de previsão ou que esteja em conformidade com a convenção scikit. Se o seu modelo não suportar isso, você pode envolver seu `predict` `predict_proba` modelo em uma função que gera o mesmo resultado que ou em Scikit e usar essa função de invólucro com o explicador selecionado. Se um pipeline for fornecido, a função de explicação pressupõe que o script de pipeline em execução retorne uma previsão. Usando esta técnica `azureml.interpret` de embrulho, pode suportar modelos treinados via PyTorch, TensorFlow e Keras frameworks de aprendizagem profunda, bem como modelos clássicos de aprendizado de máquina.

## <a name="local-and-remote-compute-target"></a>Alvo de computação local e remota

O `azureml.interpret` pacote foi projetado para funcionar com alvos de computação local e remoto. Se for executado localmente, as funções do SDK não entrarão em contato com nenhum serviço do Azure. 

Você pode executar a explicação remotamente no Azure Machine Learning Compute e registrar as informações de explicação no Azure Machine Learning Run History Service. Uma vez que essas informações são registradas, relatórios e visualizações da explicação estão prontamente disponíveis no estúdio Azure Machine Learning para análise do usuário.


## <a name="next-steps"></a>Próximas etapas

Veja [o como fazer](how-to-machine-learning-interpretability-aml.md) para habilitar a interpretável para modelos que treinam tanto localmente quanto em recursos de computação remota do Azure Machine Learning. Consulte os [cadernos de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para obter cenários adicionais.
