---
title: 'Atribuir dados ao cluster: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Atribuir dados ao cluster no Azure Machine Learning para marcar o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477707"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: Atribuir dados a clusters

Este artigo descreve como usar o módulo *Atribuir dados a clusters* no azure Machine Learning designer (visualização). O módulo gera previsões através de um modelo de clustering que foi treinado com o algoritmo *de clustering de meios K.*

O módulo Atribuir dados a clusters retorna um conjunto de dados que contém as prováveis atribuições para cada novo ponto de dados. 

## <a name="how-to-use-assign-data-to-clusters"></a>Como atribuir dados a clusters
  
1. No designer de Machine Learning do Azure, localize um modelo de clustering previamente treinado. Você pode criar e treinar um modelo de clustering usando qualquer um dos seguintes métodos:  
  
    - Configure o algoritmo de clustering de meios K usando o módulo [de clusterde k-means](k-means-clustering.md) e treine o modelo usando um conjunto de dados e o módulo Train Clustering Model (este artigo).  
  
    - Você também pode adicionar um modelo de cluster treinado existente do grupo **Saved Models** em seu espaço de trabalho.

2. Anexar o modelo treinado à porta de entrada esquerda de **Atribuir dados a clusters**.  

3. Anexar um novo conjunto de dados como entrada. 

   Neste conjunto de dados, as etiquetas são opcionais. Geralmente, o clustering é um método de aprendizagem não supervisionado. Não se espera que você conheça as categorias com antecedência. No entanto, as colunas de entrada devem ser as mesmas das colunas que foram usadas no treinamento do modelo de clustering, ou ocorre um erro.

    > [!TIP]
    > Para reduzir o número de colunas que são escritas para o designer a partir das previsões do cluster, use [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md)e selecione um subconjunto das colunas. 
    
4. Deixe a **Verificação para anexar ou desmarcar para a** caixa de seleção de resultados selecionada se quiser que os resultados contenham o conjunto de dados de entrada completo, incluindo uma coluna que exibe os resultados (atribuições de cluster).
  
    Se você limpar esta caixa de seleção, apenas os resultados serão devolvidos. Essa opção pode ser útil quando você cria previsões como parte de um serviço web.
  
5.  Envie o oleoduto.  
  
### <a name="results"></a>Resultados

+  Para visualizar os valores no conjunto de dados, clique com o botão direito do mouse no módulo e, em seguida, selecione **Visualizar**. Ou Selecione o módulo e alterne para a guia **Saídas** no painel direito, clique no ícone do histograma nas **saídas da Porta** para visualizar o resultado.

