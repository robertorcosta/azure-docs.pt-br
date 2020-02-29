---
title: 'Detecção de anomalias baseada em PCA: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo detecção de anomalias com base em PCA para criar um modelo de detecção de anomalias baseado na análise de componente principal (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: f126eb347d11d3203ad3b97abe3606738e67c33c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925833"
---
# <a name="pca-based-anomaly-detection"></a>Detecção de anomalias com base em PCA

Este artigo descreve como usar o módulo **detecção de anomalias com base em PCA** no designer de Azure Machine Learning (versão prévia) para criar um modelo de detecção de anomalias com base na análise de componente principal (PCA).

Esse módulo ajuda você a criar um modelo em cenários em que é fácil obter dados de treinamento de uma classe, como transações válidas, mas difícil obter amostras suficientes das anomalias direcionadas. 

Por exemplo, para detectar transações fraudulentas, muitas vezes você não tem exemplos de fraudes suficientes para treinar, mas tem muitos exemplos de transações boas. O módulo **detecção de anomalias com base em PCA** resolve o problema analisando os recursos disponíveis para determinar o que constitui uma classe "normal" e a aplicação de métricas de distância para identificar casos que representam anomalias. Isso permite treinar um modelo usando dados desequilibrados existentes.

## <a name="more-about-principal-component-analysis"></a>Mais informações sobre Análise de Componente Principal

A *análise de componente principal*, que frequentemente é abreviada como PCA, é uma técnica estabelecida no aprendizado de máquina. O PCA é frequentemente usado na análise de dados exploratória porque revela a estrutura interna dos dados e explica a variação nos dados.

O PCA funciona analisando dados que contêm várias variáveis. Ele procura correlações entre as variáveis e determina a combinação de valores que melhor captura diferenças em resultados. Esses valores de recurso combinados são usados para criar um espaço de recurso mais compacto chamado de *componentes principais*.

Para detecção de anomalias, cada nova entrada é analisada e o algoritmo de detecção de anomalias computa sua projeção no eigenvectors, junto com um erro de reconstrução normalizado. O erro normalizado é usado como a pontuação de anomalias. Quanto maior o erro, mais anormal é a instância.

Para obter informações adicionais sobre como o PCA funciona e sobre a implementação para detecção de anomalias, consulte estes documentos:

- [Um algoritmo aleatório para análise de componente principal](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan e Tygert

- [Localizando estrutura com aleatoriedade: algoritmos probabilística para construir decomposição aproximado de matriz](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (download de PDF). Halko, Martinsson e Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Como configurar a detecção de anomalias do PCA

1. Adicione o módulo **detecção de anomalias com base em PCA** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria de **detecção de anomalias** .

2. No painel **Propriedades** do módulo **detecção de anomalias com base em PCA** , clique na opção modo de **treinamento** e indique se você deseja treinar o modelo usando um conjunto específico de parâmetros ou usar uma limpeza de parâmetro para localizar os melhores parâmetros.

    - **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.

3. **Número de componentes a serem usados no PCA**: especifique o número de recursos de saída ou componentes que você deseja gerar.

    A decisão de quantos componentes incluir é uma parte importante do design do experimento usando o PCA. As diretrizes gerais são que você não deve incluir o mesmo número de componentes do PCA que há variáveis. Em vez disso, você deve começar com um número menor de componentes e aumentá-los até que alguns critérios sejam atendidos.

    Os melhores resultados são obtidos quando o número de componentes de saída é **menor que** o número de colunas de recurso disponíveis no conjunto de recursos.

4. Especifique a quantidade de sobreamostragem a ser executada durante o treinamento aleatório do PCA. Em problemas de detecção de anomalias, os dados desequilibrados dificultam a aplicação das técnicas padrão do PCA. Ao especificar alguma quantidade de sobreamostragem, você pode aumentar o número de instâncias de destino.

    Se você especificar 1, nenhuma sobreamostração será executada. Se você especificar qualquer valor maior que 1, serão geradas amostras adicionais para usar no treinamento do modelo.

    Há duas opções, dependendo se você está usando uma varredura de parâmetro ou não:

    - **Parâmetro de sobreamostragem para o PCA aleatório**: digite um único número inteiro que representa a proporção de sobreamostragem da classe minoritária sobre a classe normal. (Disponível ao usar o método de treinamento de **parâmetro único** .)

    > [!NOTE]
    > Não é possível exibir o conjunto de dados com amostra. Para obter detalhes adicionais de como a Superamostragem é usada com o PCA, consulte [notas técnicas](#technical-notes).

5. **Habilitar normalização média de recursos de entrada**: Selecione esta opção para normalizar todos os recursos de entrada para uma média de zero. A normalização ou o dimensionamento para zero é geralmente recomendado para o PCA, pois a meta do PCA é maximizar a variação entre variáveis.

     Esta opção é selecionada por padrão. Desmarque esta opção se os valores já tiverem sido normalizados usando um método ou uma escala diferente.

6. Conecte um conjunto de uma de treinamento marcado e um dos módulos de treinamento:

    - Se você definir a opção **criar modo de instrutor** como **parâmetro único**, use o módulo [treinar modelo de detecção de anomalias](train-anomaly-detection-model.md) .

7. Execute o pipeline.

## <a name="results"></a>Resultados

Quando o treinamento estiver concluído, você poderá salvar o modelo treinado ou conectá-lo ao módulo [modelo de Pontuação](score-model.md) para prever as pontuações de anomalias.

Para avaliar os resultados de um modelo de detecção de anomalias, é necessário algumas etapas adicionais:

1. Verifique se uma coluna de pontuação está disponível em ambos os conjuntos de os

    Se você tentar avaliar um modelo de detecção de anomalias e obter o erro, "não há nenhuma coluna de pontuação no conjunto de resultados de comparação para comparar", significa que você está usando um conjunto de cálculo típico que contém uma coluna de rótulo, mas sem pontuações de probabilidade. Você precisa escolher um conjunto de um que corresponda à saída do esquema para modelos de detecção de anomalias, que inclui uma coluna de **Rótulos pontuados** e **probabilidades de Pontuação** .

2. Verifique se as colunas de rótulo estão marcadas

    Às vezes, os metadados associados à coluna de rótulo são removidos no grafo de pipeline. Se isso acontecer, quando você usar o módulo [modelo de avaliação](evaluate-model.md) para comparar os resultados de dois modelos de detecção de anomalias, você poderá obter o erro "não há nenhuma coluna de rótulo no conjunto de resultados" ou "não há nenhuma coluna de rótulo no conjunto de linha de DataSet para comparar".

    Você pode evitar esse erro adicionando o módulo [Editar metadados](edit-metadata.md) antes do módulo [modelo de avaliação](evaluate-model.md) . Use o seletor de coluna para escolher a coluna de classe e, na lista suspensa **campos** , selecione **rótulo**.

3. Use [Executar script Python](execute-python-script.md) para ajustar as categorias de coluna de rótulo como 1 (positivo, normal) e 0 (negativo, anormal).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Observações técnicas

Esse algoritmo usa o PCA para aproximar o subespaço que contém a classe normal. O subespaço é estendido por eigenvectors associado ao eigenvalues superior da matriz de covariância de dados. Para cada nova entrada, o detector de anomalias primeiro computa sua projeção no eigenvectors e, em seguida, computa o erro de reconstrução normalizado. Esse erro é a pontuação de anomalias. Quanto maior o erro, mais anômala é a instância. Para obter detalhes sobre como o espaço normal é computado, consulte Wikipédia: [análise de componente principal](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer (versão prévia)](designer-error-codes.md) para obter uma lista de erros específicos para os módulos do designer.