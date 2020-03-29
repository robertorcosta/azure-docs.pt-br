---
title: 'Avaliar recomendador: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Avaliador no Azure Machine Learning para avaliar a precisão das previsões do modelo recomendador.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312253"
---
# <a name="evaluate-recommender"></a>Avaliar o sistema de recomendação

Este artigo descreve como usar o módulo Avaliar recomendador no designer azure Machine Learning (visualização). O objetivo é medir a precisão das previsões que um modelo de recomendação fez. Usando este módulo, você pode avaliar diferentes tipos de recomendações:  
  
-   Classificações previstas para um usuário e um item    
-   Itens recomendados para um usuário  
  
Quando você cria previsões usando um modelo de recomendação, resultados ligeiramente diferentes são devolvidos para cada um desses tipos de previsão suportadas. O módulo Avaliar Recomendador deduz o tipo de previsão a partir do formato da coluna do conjunto de dados pontuado. Por exemplo, o conjunto de dados pontuado pode conter:

- Triplos da classificação de itens do usuário
- Usuários e seus itens recomendados

O módulo também aplica as métricas de desempenho apropriadas, com base no tipo de previsão que está sendo feita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Como configurar O Recomendador avalia

O módulo Avaliar Recomendador compara a saída de previsão usando um modelo de recomendação com os dados correspondentes de "verdade de solo". Por exemplo, o módulo [Score SVD Recommender](score-svd-recommender.md) produz conjuntos de dados pontuados que você pode analisar usando O Avaliador.

### <a name="requirements"></a>Requisitos

O Recommender requer os seguintes conjuntos de dados como entrada. 
  
#### <a name="test-dataset"></a>Conjunto de dados de teste

O conjunto de dados do teste contém os dados de "verdade terrestre" na forma de triplos de classificação de item do usuário.  

#### <a name="scored-dataset"></a>Conjunto de dados classificado

O conjunto de dados pontuado contém as previsões que o modelo de recomendação gerou.  
  
As colunas neste segundo conjunto de dados dependem do tipo de previsão que você realizou durante o processo de pontuação. Por exemplo, o conjunto de dados pontuado pode conter qualquer um dos seguintes:

- Usuários, itens e as classificações que o usuário provavelmente daria para o item
- Uma lista de usuários e itens recomendados para eles 

### <a name="metrics"></a>Métricas

As métricas de desempenho para o modelo são geradas com base no tipo de entrada. As seções a seguir dão detalhes.

## <a name="evaluate-predicted-ratings"></a>Avalie as classificações previstas  

Ao avaliar as classificações previstas, o conjunto de dados pontuados (a segunda entrada para o Avaliador) deve conter triplos de classificação de item do usuário que atendam a esses requisitos:
  
-   A primeira coluna do conjunto de dados contém os identificadores do usuário.    
-   A segunda coluna contém os identificadores do item.  
-   A terceira coluna contém as classificações correspondentes do item do usuário.  
  
> [!IMPORTANT] 
> Para que a avaliação seja `User`bem `Item`sucedida, os nomes das colunas devem ser , e `Rating`, respectivamente.  
  
O Assess Recommender compara as classificações no conjunto de dados "verdade terrestre" com as classificações previstas do conjunto de dados pontuados. Em seguida, calcula o erro absoluto médio (MAE) e o erro quadrado médio da raiz (RMSE).



## <a name="evaluate-item-recommendations"></a>Avaliar recomendações de itens

Ao avaliar as recomendações de itens, use um conjunto de dados pontuado que inclua os itens recomendados para cada usuário:
  
-   A primeira coluna do conjunto de dados deve conter o identificador do usuário.    
-   Todas as colunas subseqüentes devem conter os respectivos identificadores de itens recomendados, ordenados pelo quão relevante um item é para o usuário. 

Antes de conectar este conjunto de dados, recomendamos que você classifique o conjunto de dados para que os itens mais relevantes venham primeiro.  

> [!IMPORTANT] 
> Para que o Recomendador avalie `User`para `Item 1` `Item 2`funcionar, os nomes das colunas devem ser , e `Item 3` assim por diante.  
  
O Recommender avalia o ganho acumulado normalizado normalizado (NDCG) e o devolve no conjunto de dados de saída.  
  
Como é impossível saber a "verdade terrestre" real para os itens recomendados, o Assess Recommender usa as classificações de item do usuário no conjunto de dados do teste como ganhos no cálculo do NDCG. Para avaliar, o módulo de pontuação recomendador deve produzir apenas recomendações para itens com classificações de "verdade de solo" (no conjunto de dados do teste).  
  

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
