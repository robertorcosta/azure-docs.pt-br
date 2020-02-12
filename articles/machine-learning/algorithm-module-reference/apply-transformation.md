---
title: 'Aplicar transformação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo aplicar transformação em Azure Machine Learning para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137578"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.  
  
Por exemplo, se você usou pontuações z para normalizar seus dados de treinamento usando o módulo **normalizar dados** , convém usar o valor de pontuação z que foi calculado para treinamento durante a fase de Pontuação também. No Azure Machine Learning, você pode salvar o método de normalização como uma transformação e, em seguida, usar **aplicar transformação** para aplicar a pontuação z aos dados de entrada antes da pontuação.
  
Azure Machine Learning fornece suporte para criar e, em seguida, aplicar vários tipos diferentes de transformações personalizadas. Por exemplo, talvez você queira salvar e reutilizar transformações em:  
  
- Remover ou substituir valores ausentes, usando **limpar dados ausentes**
- Normalizar dados, usando **dados normalizados**
  

## <a name="how-to-use-apply-transformation"></a>Como usar aplicar transformação  
  
1. Adicione o módulo **aplicar transformação** ao seu pipeline. Você pode encontrar esse módulo na **classificação do modelo &** categoria de avaliação. 
  
2. Localize uma transformação existente para usar como uma entrada. As transformações salvas anteriormente podem ser encontradas no grupo **meus conjuntos de valores** na categoria **conjuntos de valores** na árvore de módulos à esquerda.  
  
   
  
3. Conecte o conjunto de um que você deseja transformar. O DataSet deve ter exatamente o mesmo esquema (número de colunas, nomes de coluna, tipos de dados) que o conjunto para o qual a transformação foi criada primeiro.  
  
4. Nenhum outro parâmetro precisa ser definido, pois toda a personalização é feita ao definir a transformação.  
  
5. Para aplicar uma transformação ao novo conjunto de um, execute o pipeline.  

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 