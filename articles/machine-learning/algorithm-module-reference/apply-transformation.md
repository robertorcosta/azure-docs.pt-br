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
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898869"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.

Por exemplo, se você usou pontuações z para normalizar seus dados de treinamento usando o módulo **normalizar dados** , convém usar o valor de pontuação z que foi calculado para treinamento durante a fase de Pontuação também. No Azure Machine Learning, você pode salvar o método de normalização como uma transformação e, em seguida, usar **aplicar transformação** para aplicar a pontuação z aos dados de entrada antes da pontuação.

## <a name="how-to-save-transformations"></a>Como salvar transformações

O designer permite salvar transformações de dados como **DataSets** para que você possa usá-los em outros pipelines.

1. Selecione um módulo de transformação de dados que foi executado com êxito.

1. Selecione a guia **saídas + logs** .

1. Localize a saída da transformação e selecione o **conjunto de registros** para salvá-lo como um módulo na categoria conjuntos de banco de **valores** na paleta do módulo.

## <a name="how-to-use-apply-transformation"></a>Como usar aplicar transformação  
  
1. Adicione o módulo **aplicar transformação** ao seu pipeline. Você pode encontrar esse módulo na seção **classificação do modelo & avaliação** da paleta do módulo. 
  
1. Localize a transformação salva que você deseja usar em **conjuntos de valores** na paleta de módulos.

1. Conecte a saída da transformação salva à porta de entrada à esquerda do módulo **aplicar transformação** .

    O DataSet deve ter exatamente o mesmo esquema (número de colunas, nomes de coluna, tipos de dados) que o conjunto para o qual a transformação foi criada primeiro.  
  
1. Conecte a saída do conjunto de dados do módulo desejado à porta de entrada à direita do módulo **aplicar transformação** .
  
1. Para aplicar uma transformação ao novo conjunto de um, execute o pipeline.  

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 