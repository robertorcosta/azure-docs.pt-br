---
title: 'Aplicar transformação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Aplicar Transformação no Azure Machine Learning para modificar um conjunto de dados de entrada com base em uma transformação previamente computada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395252"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação previamente computada.

Por exemplo, se você usou z-scores para normalizar seus dados de treinamento usando o módulo **Normalize Data,** você gostaria de usar o valor de pontuação z que foi calculado para treinamento durante a fase de pontuação também. No Azure Machine Learning, você pode salvar o método de normalização como uma transformação e, em seguida, usar **Aplicar transformação** para aplicar a pontuação z aos dados de entrada antes de marcar.

## <a name="how-to-save-transformations"></a>Como salvar transformações

O designer permite salvar transformações de dados como **conjuntos de dados para** que você possa usá-los em outros pipelines.

1. Selecione um módulo de transformação de dados que tenha sido executado com sucesso.

1. Selecione a guia **Saídas + logs.**

1. Selecione o **ícone Salvar** para salvar a **transformação de resultados**.

## <a name="how-to-use-apply-transformation"></a>Como usar aplicar transformação  
  
1. Adicione o módulo **Aplicar transformação** ao seu pipeline. Você pode encontrar este módulo na seção **de avaliação de & pontuação de modelo** da paleta do módulo. 
  
1. Encontre a transformação salva que deseja usar em **Conjuntos de** > **dados Meus conjuntos de dados** na paleta de módulos.

1. Conecte a saída da transformação salva à porta de entrada esquerda do módulo **Aplicar transformação.**

    O conjunto de dados deve ter exatamente o mesmo esquema (número de colunas, nomes de colunas, tipos de dados) como o conjunto de dados para o qual a transformação foi projetada pela primeira vez.  
  
1. Conecte a saída do conjunto de dados do módulo desejado à porta de entrada correta do módulo **Aplicar transformação.**
  
1. Para aplicar uma transformação ao novo conjunto de dados, execute o pipeline.  

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 