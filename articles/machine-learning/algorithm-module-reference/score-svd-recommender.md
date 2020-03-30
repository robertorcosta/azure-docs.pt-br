---
title: 'Recomendador de Pontuação SVD: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo SVD Recommender score no Azure Machine Learning para marcar previsões de recomendação para um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455971"
---
# <a name="score-svd-recommender"></a>Pontuar o sistema de recomendação SVD

Este artigo descreve como usar o módulo Score SVD Recommender no azure Machine Learning designer (visualização). Use este módulo para criar previsões usando um modelo de recomendação treinado baseado no algoritmo SVD (Single Value Decomposition).

O recomendador SVD pode gerar dois tipos diferentes de previsões:

- [Prever as classificações de um determinado usuário e item](#prediction-of-ratings)
- [Itens recomendados a um usuário](#recommendations-for-users)

Quando você está criando o segundo tipo de previsões, você pode operar em um desses modos:

- **O modo de produção** considera todos os usuários ou itens. É normalmente usado em um serviço web.

  Você pode criar pontuações para novos usuários, não apenas usuários vistos durante o treinamento. Para obter mais informações, consulte as [notas técnicas](#technical-notes). 

- **O modo de avaliação** opera em um conjunto reduzido de usuários ou itens que podem ser avaliados. É normalmente usado durante operações de oleodutos.

Para obter mais informações sobre o algoritmo recomendador de SVD, consulte o artigo de pesquisa [Técnicas de fatoração de matriz para sistemas recomendadores](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Como configurar o Recommender Score SVD

Este módulo suporta dois tipos de previsões, cada uma com requisitos diferentes. 

###  <a name="prediction-of-ratings"></a>Previsão de classificações

Quando você prevê classificações, o modelo calcula como um usuário reagirá a um determinado item, dado os dados de treinamento. Os dados de entrada para pontuação devem fornecer tanto um usuário quanto o item a ser taxado.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado.** Você deve criar o modelo usando o módulo [Train SVD Recommender.](train-SVD-recommender.md)

2. Para **o tipo de previsão recomendadora,** selecione Previsão de **Classificação**. Não são necessários outros parâmetros.

3. Adicione os dados para os quais você deseja fazer previsões e conecte-os ao **Dataset para marcar**.

   Para que o modelo preveja as classificações, o conjunto de dados de entrada deve conter pares de itens de usuário.

   O conjunto de dados pode conter uma terceira coluna opcional de classificações para o par de itens de usuário na primeira e segunda colunas. Mas a terceira coluna será ignorada durante a previsão.

4. Envie o oleoduto.

### <a name="results-for-rating-predictions"></a>Resultados para previsões de classificação 

O conjunto de dados de saída contém três colunas: usuários, itens e a classificação prevista para cada usuário de entrada e item.

###  <a name="recommendations-for-users"></a>Recomendações para usuários 

Para recomendar itens para os usuários, você fornece uma lista de usuários e itens como entrada. A partir desses dados, o modelo utiliza seu conhecimento sobre itens e usuários existentes para gerar uma lista de itens com provável apelo a cada usuário. Você pode personalizar o número de recomendações retornadas. E você pode definir um limite para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado.**  Você deve criar o modelo usando o módulo [Train SVD Recommender.](train-svd-recommender.md)

2. Para recomendar itens para uma lista de usuários, defina **o tipo de previsão do Recommender** como **Recomendação de Itens**.

3. Para **a seleção recomendada de itens,** indique se você está usando o módulo de pontuação na produção ou para avaliação do modelo. Escolha um desses valores:

    - **De Todos os Itens**: Selecione esta opção se você estiver configurando um pipeline para usar em um serviço web ou em produção.  Esta opção permite o *modo de produção*. O módulo faz recomendações de todos os itens vistos durante o treinamento.

    - **A partir de Itens Avaliados (para avaliação do modelo):** Selecione esta opção se você estiver desenvolvendo ou testando um modelo. Esta opção permite o *modo de avaliação*. O módulo faz recomendações somente daqueles itens do conjunto de dados de entrada que foram avaliados.
    
    - **De Itens Não Avaliados (para sugerir novos itens aos usuários)**: Selecione esta opção se você quiser que o módulo faça recomendações apenas daqueles itens do conjunto de dados de treinamento que não foram classificados. 

4. Adicione o conjunto de dados para o qual você deseja fazer previsões e conecte-o ao **Conjunto de Dados para marcar**.

    - Para **De todos os itens,** o conjunto de dados de entrada deve consistir em uma coluna. Ele contém os identificadores dos usuários para os quais fazer recomendações.

      O conjunto de dados pode incluir duas colunas extras de identificadores de itens e classificações, mas essas duas colunas são ignoradas. 

    - Para **itens classificados (para avaliação do modelo),** o conjunto de dados de entrada deve consistir em pares de itens de usuário. A primeira coluna deve conter o identificador do usuário. A segunda coluna deve conter os identificadores de itens correspondentes.

      O conjunto de dados pode incluir uma terceira coluna de classificações de itens de usuário, mas esta coluna é ignorada.

    - Para **A partir de itens não classificados (para sugerir novos itens aos usuários)**, o conjunto de dados de entrada deve consistir em pares de itens de usuário. A primeira coluna deve conter o identificador do usuário. A segunda coluna deve conter os identificadores de itens correspondentes.

     O conjunto de dados pode incluir uma terceira coluna de classificações de itens de usuário, mas esta coluna é ignorada.

5. **Número máximo de itens a serem recomendados a um usuário**: Digite o número de itens a retornar para cada usuário. Por padrão, o módulo recomenda cinco itens.

6. **Tamanho mínimo do pool de recomendação por usuário**: Digite um valor que indique quantas recomendações anteriores são necessárias. Por padrão, este parâmetro é definido como 2, o que significa que pelo menos outros dois usuários recomendaram o item.

   Use esta opção somente se estiver pontuando no modo de avaliação. A opção não está disponível se você selecionar **De todos os itens** ou de itens não **avaliados (para sugerir novos itens aos usuários)**.

7.  Para **A partir de itens não classificados (para sugerir novos itens aos usuários)**, use a terceira porta de entrada, chamada Dados de **Treinamento,** para remover itens que já foram classificados dos resultados da previsão.

    Para aplicar este filtro, conecte o conjunto de dados de treinamento original à porta de entrada.

8. Envie o oleoduto.

### <a name="results-of-item-recommendation"></a>Resultados da recomendação do item

O conjunto de dados pontuados retornado pelo Score SVD Recommender lista os itens recomendados para cada usuário:

- A primeira coluna contém os identificadores do usuário.
- Uma série de colunas adicionais são geradas, dependendo do valor definido para **o número máximo de itens a recomendar a um usuário**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas por afinidade usuário-item. O item com maior afinidade é colocado na coluna **Item 1**.

> [!WARNING]
> Você não pode avaliar este conjunto de dados pontuados usando o módulo [Avaliar recomendador.](evaluate-recommender.md)


##  <a name="technical-notes"></a>Observações técnicas

Se você tem um pipeline com o recomendador SVD, e você move o modelo para a produção, esteja ciente de que existem diferenças importantes entre usar o recomendador no modo de avaliação e usá-lo no modo de produção.

A avaliação, por definição, exige previsões que podem ser verificadas em relação a *verdades* em um conjunto de teste. Ao avaliar o recomendador, ele deve prever apenas os itens que foram avaliados no conjunto de testes. Isso restringe os possíveis valores previstos.

Quando você operacionaliza o modelo, você normalmente altera o modo de previsão para fazer recomendações com base em todos os itens possíveis, a fim de obter as melhores previsões. Para muitas dessas previsões, não há verdade terrestre correspondente. Assim, a precisão da recomendação não pode ser verificada da mesma forma que durante as operações do gasoduto.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
