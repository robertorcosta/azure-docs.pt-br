---
title: 'Adicionar linhas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Adicionar linhas no Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477724"
---
# <a name="add-rows-module"></a>Adicionar módulo de linhas

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para concatenar dois conjuntos de dados. Na concatenação, as linhas do segundo conjunto de dados são adicionadas ao final do primeiro conjunto de dados.  
  
Concatenação de linhas é útil em cenários como estes:  
  
+ Você gerou uma série de estatísticas de avaliação e deseja combiná-las em uma tabela para criar relatórios com mais facilidade.  
  
+ Você tem trabalhado com conjuntos de dados diferentes e deseja combinar os conjuntos de dados para criar um conjunto de dados final.  

## <a name="how-to-use-add-rows"></a>Como usar adicionar linhas  

Para concatenar linhas de dois conjuntos de dados, as linhas devem ter exatamente o mesmo esquema. Isso significa, o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arraste o módulo **Adicionar linhas** para o pipeline, você pode encontrá-lo em **Transformação de Dados**.

2. Conecte os conjuntos de dados para as duas portas de entrada. O conjunto de dados que você deseja acrescentar deve estar conectado à segunda porta (à direita). 
  
3.  Envie o oleoduto. O número de linhas no conjunto de dados de saída deve igualar a soma das linhas de ambos os conjuntos de dados de entrada.

    Se você adicionar o mesmo conjunto de dados a ambas as entradas do módulo **Adicionar linhas,** o conjunto de dados será duplicado. 

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 