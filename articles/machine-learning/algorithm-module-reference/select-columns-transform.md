---
title: 'Selecionar colunas Transformar: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Select Columns Transform no Azure Machine Learning para criar uma transformação que selecione o mesmo subconjunto de colunas que no conjunto de dados dado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455903"
---
# <a name="select-columns-transform"></a>Selecionar colunas para transformação

Este artigo descreve como usar o módulo Select Columns Transform no azure Machine Learning designer (visualização). O objetivo do módulo Select Columns Transform é garantir que um conjunto previsível e consistente de colunas seja usado em operações de aprendizado de máquina a jusante.

Este módulo é útil para tarefas como pontuação, que requerem colunas específicas. Alterações nas colunas disponíveis podem quebrar o pipeline ou alterar os resultados.

Você usa Selecionar colunas Transformar para criar e salvar um conjunto de colunas. Em seguida, use o módulo [Aplicar transformação](apply-transformation.md) para aplicar essas seleções a novos dados.

## <a name="how-to-use-select-columns-transform"></a>Como usar a Select Columns Transform

Este cenário pressupõe que você deseja usar a seleção de recursos para gerar um conjunto dinâmico de colunas que serão usadas para treinar um modelo. Para garantir que as seleções de colunas sejam as mesmas para o processo de pontuação, você usa o módulo Select Columns Transform para capturar as seleções de colunas e aplicá-las em outros lugares do pipeline.

1. Adicione um conjunto de dados de entrada ao seu pipeline no designer.

2. Adicione uma instância de [seleção](filter-based-feature-selection.md)de recursos baseado em filtro .

3. Conecte os módulos e configure o módulo de seleção de recursos para encontrar automaticamente uma série de melhores recursos no conjunto de dados de entrada.

4. Adicione uma instância do Modelo de [Trem](train-model.md) e use a saída de [Seleção](filter-based-feature-selection.md) de Recursos Baseados em Filtro como entrada para treinamento.

    > [!IMPORTANT]
    > Como a importância do recurso é baseada nos valores da coluna, você não pode saber com antecedência quais colunas podem estar disponíveis para entrada no [Train Model](train-model.md).  
5. Anexar uma instância do módulo 'Transformar colunas selecionadas'. 

    Essa etapa gera uma seleção de colunas como uma transformação que pode ser salva ou aplicada a outros conjuntos de dados. Esta etapa garante que as colunas identificadas na seleção de recursos sejam salvas para outros módulos a serem reutilizados.

6. Adicione o módulo [Score Model.](score-model.md) 

   *Não conecte o conjunto de dados de entrada.* Em vez disso, adicione o módulo [Aplicar transformação](apply-transformation.md) e conecte a saída da transformação de seleção de recursos.

   > [!IMPORTANT]
   > Você não pode esperar aplicar a [seleção de recursos baseados em filtro seleção](filter-based-feature-selection.md) ao conjunto de dados de pontuação e obter os mesmos resultados. Como a seleção de recursos é baseada em valores, ela pode escolher um conjunto diferente de colunas, o que faria com que a operação de pontuação falhasse.
7. Envie o oleoduto.

Este processo de salvar e, em seguida, aplicar uma seleção de coluna garante que o mesmo esquema de dados esteja disponível para treinamento e pontuação.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
