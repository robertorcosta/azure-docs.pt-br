---
title: 'Adicionar colunas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Adicionar colunas no Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456771"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para concatenar dois conjuntos de dados. Você combina todas as colunas dos dois conjuntos de dados que você especifica como entradas para criar um único conjunto de dados. Se você precisar concatenar mais de dois conjuntos de dados, use várias instâncias de **Adicionar colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicione o módulo **Adicionar colunas** ao seu pipeline.

2. Conecte os dois conjuntos de dados que deseja concatenar. Se você quiser combinar mais de dois conjuntos de dados, você pode acorrentar várias combinações de **Adicionar colunas**.

    - É possível combinar duas colunas que possuem um número diferente de linhas. O conjunto de dados de saída é acolchoado com valores ausentes para cada linha na coluna de origem menor.

    - Você não pode escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de dados são concatenadas quando você usa **Adicionar colunas**. Portanto, se você quiser adicionar apenas um subconjunto das colunas, use Selecionar colunas no conjunto de dados para criar um conjunto de dados com as colunas desejadas.

3. Envie o oleoduto.

### <a name="results"></a>Resultados
Depois que o gasoduto for executado:

- Para ver as primeiras linhas do novo conjunto de dados, clique com o botão direito do mouse no módulo **Adicionar colunas** e selecione Visualizar. Ou Selecione o módulo e alterne para a guia **Saídas** no painel direito, clique no ícone do histograma nas **saídas da Porta** para visualizar o resultado.

O número de colunas no novo conjunto de dados é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se houver duas colunas com o mesmo nome nos conjuntos de dados de entrada, um sufixo numérico será adicionado ao nome da coluna. Por exemplo, se houver duas instâncias de uma coluna chamada TargetOutcome, a coluna esquerda será renomeada TargetOutcome_1 e a coluna direita será renomeada TargetOutcome_2.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 