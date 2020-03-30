---
title: 'Modelo de agrupamento de trens: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Modelo de Clustering de Trem no Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477418"
---
# <a name="train-clustering-model"></a>Treinar Modelo de Clustering

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para treinar um modelo de clustering.

O módulo pega um modelo de clustering não treinado que você já configurou usando o módulo [de agrupamento de meios K](k-means-clustering.md) e treina o modelo usando um conjunto de dados rotulado ou não rotulado. O módulo cria um modelo treinado que você pode usar para previsão, e um conjunto de atribuições de cluster para cada caso nos dados de treinamento.

> [!NOTE]
> Um modelo de clustering não pode ser treinado usando o módulo [Train Model,](train-model.md) que é o módulo genérico para treinamento de modelos de aprendizagem de máquina. Isso porque [o Train Model](train-model.md) trabalha apenas com algoritmos de aprendizagem supervisionados. K-means e outros algoritmos de clustering permitem aprendizado não supervisionado, o que significa que o algoritmo pode aprender com dados não rotulados.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo de clustering de trem  

1.  Adicione o módulo **Modelo de Clustering de Trem** ao seu pipeline no designer. Você pode encontrar o módulo em **Módulos de Aprendizagem de Máquina,** na categoria **Trem.**  
  
2. Adicione o módulo [de cluster de meios K](k-means-clustering.md) ou outro módulo personalizado que crie um modelo de clustercompatível e defina os parâmetros do modelo de clustering.  
    
3.  Anexar um conjunto de dados de treinamento à entrada à direita do **Train Clustering Model**.
  
5.  Em **'Conjunto de colunas',** selecione as colunas do conjunto de dados a serem usados em clusters de construção. Certifique-se de selecionar colunas que fazem bons recursos: por exemplo, evite usar IDs ou outras colunas que tenham valores únicos ou colunas que tenham todos os mesmos valores.

    Se um rótulo estiver disponível, você pode usá-lo como um recurso ou deixá-lo de fora.  
  
6. Selecione a opção, **Verifique se há apêndice ou desmarque apenas o resultado,** se quiser produzir os dados de treinamento juntamente com o novo rótulo de cluster.

    Se você desmarcar essa opção, apenas as atribuições do cluster serão a saída. 

7. Envie o pipeline ou clique no módulo **Modelo de Agrupamento de Trens** e selecione Executar **selecionado**.  
  
### <a name="results"></a>Resultados

Após o treinamento ter concluído:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

+ Para gerar pontuações a partir do modelo, use [Atribuir dados a clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 