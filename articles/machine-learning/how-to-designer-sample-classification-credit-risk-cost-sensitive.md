---
title: 'Designer: exemplo de previsão do risco de crédito'
titleSuffix: Azure Machine Learning
description: Crie um classificador e use scripts personalizados do Python para prever o risco de crédito usando o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964593"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Crie um classificador e use scripts do Python para prever o risco de crédito usando o designer do Azure Machine Learning

**Amostra 4 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Este artigo mostra como criar um pipeline complexo de aprendizado de máquina usando o designer (versão prévia). Você aprenderá a implementar uma lógica personalizada usando scripts do Python e comparar vários modelos para escolher a melhor opção.

Esta amostra treina um classificador para prever o risco de crédito usando informações de pedido de crédito, como histórico de crédito, idade e número de cartões de crédito. No entanto, você pode aplicar os conceitos deste artigo para resolver os próprios problemas de aprendizado de máquina.

Este é o grafo concluído deste pipeline:

[![Grafo do pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 4 para abri-la.

## <a name="data"></a>Dados

Esta amostra usa o conjunto de dados Cartões de Crédito Alemães do repositório do UC Irvine. Ele contém 1.000 amostras com 20 recursos e um rótulo. Cada amostra representa uma pessoa. Os 20 recursos incluem recursos numéricos e categóricos. Para obter mais informações sobre o conjunto de dados, confira o [site do UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). A última coluna é o rótulo, que indica o risco de crédito e só tem dois valores possíveis: alto risco de crédito = 2 e baixo risco de crédito = 1.

## <a name="pipeline-summary"></a>Resumo do pipeline

Neste pipeline, você vai comparar duas abordagens diferentes de geração de modelos para resolver este problema:

- Treinamento com o conjunto de dados original.
- Treinamento com um conjunto de dados replicado.

Com ambas as abordagens, você avaliará os modelos usando o conjunto de dados de teste com replicação para garantir que os resultados sejam alinhados com a função de custo. Teste dois classificadores com ambas as abordagens: **Computador de Vetor de Suporte de Duas Classes** e **Árvore de Decisão Aumentada de Duas Classes**.

O custo da classificação incorreta de um exemplo de baixo risco como alto é 1, e o custo da classificação incorreta de um exemplo de alto risco como baixo é 5. Usamos um módulo **Executar Script do Python** para considerar esse custo de classificação incorreta.

Este é o grafo do pipeline:

[![Grafo do pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Processamento de dados

Comece usando o módulo **Editor de Metadados** para adicionar nomes de colunas e substituir os nomes de colunas padrão por nomes mais significativos, obtidos na descrição do conjunto de dados no site do UCI. Forneça os novos nomes de colunas como valores separados por vírgula no campo do nome da **Nova coluna** do **Editor de Metadados**.

Em seguida, gere os conjuntos de treinamento e teste usados para desenvolver o modelo de previsão de risco. Divida o conjunto de dados original em conjuntos de treinamento e teste do mesmo tamanho usando o módulo **Dividir Dados**. Para criar conjuntos de tamanho igual, defina a opção **Fração de linhas no primeiro conjunto de dados de saída** como 0,7.

### <a name="generate-the-new-dataset"></a>Gerar o novo conjunto de dados

Como o custo de subestimação do risco é alto, defina o custo de classificação incorreta da seguinte maneira:

- Para casos de alto risco classificados incorretamente como de baixo risco: 5
- Para casos de baixo risco classificados incorretamente como de alto risco: 1

Para refletir essa função de custo, gere um novo conjunto de dados. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, mas o número de exemplos de baixo risco não é alterado. Divida os dados em conjuntos de dados de treinamento e teste antes da replicação para impedir que a mesma linha apareça em ambos os conjuntos.

Para replicar os dados de alto risco, coloque este código do Python em um módulo **Executar Script do Python**:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

O módulo **Executar Script do Python** replica os conjuntos de dados de treinamento e teste.

### <a name="feature-engineering"></a>Engenharia de recursos

O algoritmo **Computador de Vetor de Suporte de Duas Classes** exige dados normalizados. Portanto, use o módulo **Normalizar Dados** para normalizar os intervalos de todos os recursos numéricos com uma transformação `tanh`. Uma transformação `tanh` converte todos os recursos numéricos em valores dentro de um intervalo de 0 e 1, preservando a distribuição geral dos valores.

O módulo **Computador de Vetor de Suporte de Duas Classes** processa os recursos de cadeia de caracteres, convertendo-os para recursos categóricos e, em seguida, em recursos binários com um valor igual a zero ou um. Portanto, não é necessário normalizar esses recursos.

## <a name="models"></a>Modelos

Como você aplicou dois classificadores, **Computador de Vetor de Suporte de Duas Classes** (SVM) e **Árvore de Decisão Aumentada de Duas Classes**, e dois conjuntos de dados, você vai gerar um total de quatro modelos:

- SVM treinado com os dados originais.
- SVM treinado com os dados replicados.
- Árvore de Decisão Aumentada treinada com os dados originais.
- Árvore de Decisão Aumentada treinada com os dados replicados.

Esta amostra usa o fluxo de trabalho de ciência de dados padrão para criar, treinar e testar os modelos:

1. Inicialize os algoritmos de aprendizado usando **Computador de Vetor de Suporte de Duas Classes** e **Árvore de Decisão Aumentada de Duas Classes**.
1. Use o módulo **Treinar Modelo** para aplicar o algoritmo aos dados e criar o modelo real.
1. Use o módulo **Pontuar Modelo** para produzir pontuações usando os exemplos de teste.

O diagrama a seguir mostra uma parte desse pipeline, na qual os conjuntos de treinamento original e replicado são usados para treinar dois modelos de SVM diferentes. O módulo **Treinar Modelo** está conectado ao conjunto de treinamento, e o módulo **Pontuar Modelo** está conectado ao conjunto de teste.

![Grafo do pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

No estágio de avaliação do pipeline, você calculará a precisão de cada um dos quatro modelos. Para esse pipeline, use o módulo **Avaliar Modelo** para comparar exemplos que tenham o mesmo custo de classificação incorreta.

O módulo **Avaliar Modelo** pode calcular as métricas de desempenho para até dois modelos pontuados. Portanto, você pode usar uma instância de **Avaliar Modelo** para avaliar os dois modelos de SVM e outra instância de **Avaliar Modelo** para avaliar os dois modelos de Árvore de Decisão Aumentada.

Observe que o conjunto de dados de teste replicado é usado como a entrada de **Pontuar Modelo**. Em outras palavras, as pontuações de precisão final incluem o custo da classificação incorreta dos rótulos.

## <a name="combine-multiple-results"></a>Combinar vários resultados

O módulo **Avaliar Modelo** produz uma tabela com uma só linha que contém várias métricas. Para criar um só conjunto de resultados de precisão, primeiro usamos **Adicionar Linhas** para combinar os resultados em uma só tabela. Em seguida, usamos o seguinte script do Python no módulo **Executar Script do Python** para adicionar o nome do modelo e a abordagem de treinamento a cada linha na tabela de resultados:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Resultados

Para ver os resultados do pipeline, clique com o botão direito do mouse na saída Visualizar do último módulo **Selecionar Colunas no Conjunto de Dados**.

![Visualizar saída](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

A primeira coluna lista o algoritmo de aprendizado de máquina usado para gerar o modelo.

A segunda coluna indica o tipo do conjunto de treinamento.

A terceira coluna contém o valor de precisão sensível ao custo.

Com base nesses resultados, você poderá ver que a melhor precisão é fornecida pelo modelo que foi criado com o **Computador de Vetor de Suporte de Duas Classes** e treinado no conjunto de dados de treinamento replicado.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:

- [Amostra 1: regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2: regressão: comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3: classificação com seleção de recursos: previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 5: classificação: prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 6: classificação: prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7: classificação de textos: conjunto de dados SP 500 da Wikipédia](how-to-designer-sample-text-classification.md)
