---
title: 'Recomendador de Trem SVD: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Train SVD Recommender no Azure Machine Learning para treinar um recomendador bayesiano usando o algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477401"
---
# <a name="train-svd-recommender"></a>Treinar o sistema de recomendação SVD

Este artigo descreve como usar o módulo Train SVD Recommender no azure Machine Learning designer (visualização). Use este módulo para treinar um modelo de recomendação baseado no algoritmo SVD (Single Value Decomposition).  

O módulo Train SVD Recommender lê um conjunto de dados de triplos de classificação de itens do usuário. Ele retorna um recomendador SVD treinado. Em seguida, você pode usar o modelo treinado para prever classificações ou gerar recomendações, usando o módulo [Recommender Score SVD.](score-svd-recommender.md)  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mais sobre modelos de recomendação e o recomendador svd  

O principal objetivo de um sistema de recomendação é recomendar um ou mais *itens* aos *usuários* do sistema. Exemplos de um item podem ser um filme, restaurante, livro ou música. Um usuário pode ser uma pessoa, um grupo de pessoas ou outra entidade com preferências de itens.  

Existem duas abordagens principais para sistemas de recomendação: 

+ Uma abordagem **baseada em conteúdo** faz uso de recursos para usuários e itens. Os usuários podem ser descritos por propriedades como idade e sexo. Os itens podem ser descritos por propriedades como autor e fabricante. Você pode encontrar exemplos típicos de sistemas de recomendação baseados em conteúdo em sites de matchmaking sociais. 
+ **A filtragem colaborativa** utiliza apenas identificadores dos usuários e dos itens. Ele obtém informações implícitas sobre essas entidades a partir de uma matriz (esparsa) de classificações dadas pelos usuários aos itens. Podemos aprender sobre um usuário a partir dos itens que eles avaliaram e de outros usuários que avaliaram os mesmos itens.  

O recomendador SVD usa identificadores dos usuários e dos itens, e uma matriz de classificações dadas pelos usuários aos itens. É um *recomendador colaborativo.* 

Para obter mais informações sobre o recomendador de SVD, consulte o artigo de pesquisa relevante: [Técnicas de fatoração matricial para sistemas recomendadores](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Como configurar o Train SVD Recommender  

### <a name="prepare-data"></a>Preparar dados

Antes de usar o módulo, seus dados de entrada devem estar no formato que o modelo de recomendação espera. É necessário um conjunto de dados de treinamento de triplos de classificação de itens de usuário.

+ A primeira coluna contém identificadores de usuário.
+ A segunda coluna contém identificadores de itens.
+ A terceira coluna contém a classificação para o par de itens de usuário. Os valores de classificação devem ser do tipo numérico.  

O **conjunto de** dados Movie Ratings no designer azure Machine Learning (selecione **Conjuntos de dados** e, em seguida, **Amostras)** demonstra o formato esperado:

![Classificações de filmes](media/module/movie-ratings-dataset.png)

A partir desta amostra, você pode ver que um único usuário classificou vários filmes. 

### <a name="train-the-model"></a>Treinar o modelo

1.  Adicione o módulo Train SVD Recommender ao seu pipeline no designer e conecte-o aos dados de treinamento.  
   
2.  Para **Número de fatores,** especifique o número de fatores a serem utilizados com o recomendador.  
    
    Cada fator mede o quanto o usuário está se relacionando com o item. O número de fatores é também a dimensionalidade do espaço fator latente. Com o número de usuários e itens aumentando, é melhor definir um número maior de fatores. Mas se o número for muito grande, o desempenho pode cair.
    
3.  **O número de iterações de algoritmode recomendação** indica quantas vezes o algoritmo deve processar os dados de entrada. Quanto maior esse número, mais precisas são as previsões. No entanto, um número maior significa um treinamento mais lento. O valor padrão é 30.

4.  Para **taxa de aprendizagem,** digite um número entre 0,0 e 2.0 que define o tamanho da etapa para o aprendizado.

    A taxa de aprendizado determina o tamanho da etapa em cada iteração. Se o tamanho da etapa for muito grande, você pode ultrapassar a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento leva mais tempo para encontrar a melhor solução. 
  
5.  Envie o oleoduto.  


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
