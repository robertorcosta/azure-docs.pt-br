---
title: 'Detecção de anomalia baseada em PCA: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de detecção de anomalias baseada em PCA para criar um modelo de detecção de anomalias baseado na Análise de Componentes Principais (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502978"
---
# <a name="pca-based-anomaly-detection"></a>Detecção de anomalias com base em PCA

Este artigo descreve como usar o módulo **de detecção de anomalias baseada** em PCA no azure Machine Learning designer (preview), para criar um modelo de detecção de anomalias baseado na Análise de Componentes Principais (PCA).

Este módulo ajuda você a construir um modelo em cenários onde é fácil obter dados de treinamento de uma classe, como transações válidas, mas difícil obter amostras suficientes das anomalias direcionadas. 

Por exemplo, para detectar transações fraudulentas, muitas vezes você não tem exemplos suficientes de fraude para treinar, mas tem muitos exemplos de boas transações. O módulo **de detecção de anomalias baseada em PCA** resolve o problema analisando os recursos disponíveis para determinar o que constitui uma classe "normal" e aplicando métricas de distância para identificar casos que representam anomalias. Isso permite que você treine um modelo usando dados desequilibrados existentes.

## <a name="more-about-principal-component-analysis"></a>Mais informações sobre Análise de Componente Principal

*A Análise de Componentes Principais*, que é frequentemente abreviada para PCA, é uma técnica estabelecida em aprendizagem de máquina. O PCA é frequentemente usado na análise de dados exploratória porque revela a estrutura interna dos dados e explica a variação nos dados.

O PCA trabalha analisando dados que contêm muitas variáveis. Ele procura correlações entre as variáveis e determina a combinação de valores que melhor detecta as diferenças nos resultados. Esses valores de recurso combinados são usados para criar um espaço de recurso mais compacto chamado *os componentes principais*.

Para detecção de anomalias, cada nova entrada é analisada, e o algoritmo de detecção de anomalias calcula sua projeção nos eigenvetores, juntamente com um erro de reconstrução normalizado. O erro normalizado é usado como pontuação de anomalia. Quanto maior o erro, mais anormal é a instância.

Para obter mais informações sobre como o PCA funciona e sobre a implementação para detecção de anomalias, consulte estes artigos:

- [Um algoritmo randomizado para análise de componentes principais.](https://arxiv.org/abs/0809.2274) Rokhlin, Szlan e Tygert

- [Encontrando estrutura com aleatoriedade: Algoritmos Probabilísticos para Construção de Decomções Dematrizes Aproximadas](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (download em PDF). Halko, Martinsson e Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Como configurar a detecção de anomalias pca

1. Adicione o módulo **de detecção de anomalias baseado em PCA** ao seu pipeline no designer. Você pode encontrar este módulo na categoria **Detecção de Anomalias.**

2. No painel direito do módulo de detecção de **anomalias baseada em PCA,** clique na opção **modo Treinamento** e indique se deseja treinar o modelo usando um conjunto específico de parâmetros ou use uma varredura de parâmetros para encontrar os melhores parâmetros.

    - **Parâmetro Único :** Selecione esta opção se você souber como deseja configurar o modelo e forneça um conjunto específico de valores como argumentos.

3. **Número de componentes a serem usados no PCA**: Especifique o número de recursos de saída ou componentes que deseja produzir.

    A decisão de quantos componentes incluir é uma parte importante do design do experimento usando PCA. A orientação geral é que você não deve incluir o mesmo número de componentes pca como há variáveis. Em vez disso, você deve começar com algum número menor de componentes e aumentá-los até que alguns critérios sejam atendidos.

    Os melhores resultados são obtidos quando o número de componentes de saída é **menor do que** o número de colunas de recursos disponíveis no conjunto de dados.

4. Especifique a quantidade de superamostragem a ser realizado durante o treinamento PCA randomizado. Em problemas de detecção de anomalias, dados desequilibrados dificultam a aplicação de técnicas padrão de PCA. Ao especificar alguma quantidade de superamostragem, você pode aumentar o número de instâncias-alvo.

    Se você especificar 1, nenhuma amostragem excessiva é realizada. Se você especificar qualquer valor superior a 1, amostras adicionais serão geradas para usar no treinamento do modelo.

    Existem duas opções, dependendo se você está usando uma varredura de parâmetros ou não:

    - **Parâmetro de superamostragem para PCA randomizado**: Digite um único número inteiro que representa a razão de superamostragem da classe minoritária sobre a classe normal. (Disponível ao usar o método de treinamento **de parâmetro único.)**

    > [!NOTE]
    > Não é possível visualizar o conjunto de dados sobreamostrado. Para obter mais informações sobre como a superamostragem é usada com pca, consulte [Notas técnicas](#technical-notes).

5. **Habilitar a normalização média do recurso**de entrada : Selecione esta opção para normalizar todos os recursos de entrada a uma média de zero. Normalização ou dimensionamento para zero é geralmente recomendado para PCA, porque o objetivo do PCA é maximizar a variância entre as variáveis.

     Esta opção é selecionada por padrão. Desmarque essa opção se os valores já tiverem sido normalizados usando um método ou escala diferente.

6. Conecte um conjunto de dados de treinamento marcado e um dos módulos de treinamento:

    - Se você definir a opção **Criar modo treinador** como Parâmetro **Único,** use o módulo [Modelo de Detecção de Anomalia de Trem.](train-anomaly-detection-model.md)

7. Envie o oleoduto.

## <a name="results"></a>Resultados

Quando o treinamento estiver concluído, você pode salvar o modelo treinado ou conectá-lo ao módulo [Score Model](score-model.md) para prever pontuações de anomalias.

Avaliar os resultados de um modelo de detecção de anomalias requer algumas etapas adicionais:

1. Certifique-se de que uma coluna de pontuação está disponível em ambos os conjuntos de dados

    Se você tentar avaliar um modelo de detecção de anomalia e obter o erro, "Não há coluna de pontuação no conjunto de dados pontuados para comparar", significa que você está usando um conjunto de dados de avaliação típico que contém uma coluna de rótulo, mas nenhuma pontuação de probabilidade. Você precisa escolher um conjunto de dados que corresponda à saída de esquema para modelos de detecção de anomalias, que inclui uma coluna **Marcas e Probabilidades** **Pontuadas.**

2. Certifique-se de que as colunas de etiqueta estão marcadas

    Às vezes, os metadados associados à coluna de rótulos são removidos no gráfico do pipeline. Se isso acontecer, quando você usar o módulo [Avaliar modelo](evaluate-model.md) para comparar os resultados de dois modelos de detecção de anomalias, você pode obter o erro: "Não há coluna de rótulo no conjunto de dados marcado", ou "Não há coluna de rótulo no conjunto de dados marcado para comparar".

    Você pode evitar esse erro adicionando o módulo [Editar metadados](edit-metadata.md) antes do módulo [Avaliar modelo.](evaluate-model.md) Use o seletor de coluna para escolher a coluna de classe e, na lista de parada **Campos,** selecione **'Rótulo ''Rótulo'.**

3. Use [Execute Python Script](execute-python-script.md) para ajustar as categorias da coluna de rótulos como 1 (positivo, normal) e 0 (negativo, anormal).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Observações técnicas

Este algoritmo usa PCA para aproximar o subespaço que contém a classe normal. O subespaço é estendido por eigenvetores associados aos valores superiores da matriz de covariância de dados. Para cada nova entrada, o detector de anomalias primeiro calcula sua projeção nos eigenvetores, e então calcula o erro de reconstrução normalizado. Este erro é a pontuação da anomalia. Quanto maior o erro, mais anômalo é a instância. Para obter detalhes sobre como o espaço normal é calculado, consulte Wikipedia: [Análise principal de componentes](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

Consulte [Exceções e códigos de erro para o designer (visualização)](designer-error-codes.md) para obter uma lista de erros específicos dos módulos do designer.''