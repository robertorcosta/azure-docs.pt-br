---
title: Aplicar Transformação de Imagem
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo aplicar transformação de imagem para aplicar uma transformação de imagem a um diretório de imagens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 7ff135911742c49c2c52ce30d1dca00bc89b3a56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450090"
---
# <a name="apply-image-transformation"></a>Aplicar Transformação de Imagem 

Este artigo descreve como usar o módulo aplicar transformação de imagem no designer de Azure Machine Learning (versão prévia) para modificar um diretório de imagem de entrada com base em uma transformação de imagem especificada anteriormente.  

Você precisa conectar um módulo de [transformação de imagem inicial](init-image-transformation.md) para especificar a transformação e, em seguida, aplicar essa transformação ao diretório de imagem de entrada do módulo aplicar transformação de imagem.

## <a name="how-to-use-apply-image-transformation"></a>Como usar a transformação aplicar imagem  

1. Adicione o módulo **aplicar transformação de imagem** ao seu pipeline. Você pode encontrar esse módulo na categoria *Pesquisa Visual computacional/transformação de dados de imagem* . 

2. Conecte a saída da **transformação init Image** à entrada à esquerda da **transformação aplicar imagem**.

     > [!NOTE]
     > Somente a transformação de imagem gerada pelo módulo [transformação de imagem de inicialização](init-image-transformation.md) é aceita para este módulo. Para outro tipo de transformação, conecte-a para [aplicar a transformação](apply-transformation.md); caso contrário, ' InvalidTransformationDirectoryError ' será gerado.


3. Conecte o diretório de imagem que você deseja transformar.

4. Para o **modo**, especifique para qual finalidade você usa a transformação de entrada: ' para treinamento ' ou ' para inferência '. 

   Se você selecionar **para treinamento**, toda a transformação especificada na transformação init Image será aplicada.

   Se você selecionar **para a inferência, a**transformação como criar novos exemplos aleatoriamente será excluída antes de ser aplicada. Isso ocorre porque as operações de transformação para criar novos exemplos aleatoriamente, como ' flip horizontal aleatório ', são usadas para o aumento de dados no treinamento, que deve ser removido na inferência porque amostras de inferência precisam ser corrigidas para previsão e avaliação precisas.

   > [!NOTE]
   > As transformações que serão excluídas no modo **de inferência** são: corte aleatório redimensionado, corte aleatório, flip horizontal aleatório, flip vertical aleatório, rotação aleatória, afinidade aleatória, escala de cinza aleatória, perspectiva aleatória, apagamento aleatório.

5. Para aplicar uma transformação de imagem a um novo diretório de imagens, envie o pipeline.  

### <a name="module-parameters"></a>Parâmetros do módulo

| Nome | Intervalo | Type | Padrão                   | Descrição                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Qualquer   | Mode | (Exigir que o usuário especifique) | Para qual finalidade você usa a transformação de entrada. Você deve excluir operações de transformação ' Random ' na inferência, mas mantê-las em treinamento |

### <a name="expected-inputs"></a>Entradas esperadas  

| Nome                       | Tipo                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformação imagem de entrada | TransformationDirectory | Transformação imagem de entrada        |
| Diretório da imagem de entrada      | ImageDirectory          | Diretório de imagem a ser transformado |

### <a name="outputs"></a>Saídas  

| Nome                   | Tipo           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Diretório de imagens de saída | ImageDirectory | Diretório de imagens de saída |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
