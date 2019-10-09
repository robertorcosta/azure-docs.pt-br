---
title: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029334"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory



Use a pesquisa para adicionar dados de referência de outra fonte a seu fluxo de dados. A transformação de pesquisa requer a definição de uma fonte que aponte para a sua tabela de referência e apresente correspondência com os campos de chave.

![Transformação de pesquisa](media/data-flow/lookup1.png "Pesquisa")

Selecione os campos de chave que você deseja comparar entre os campos de fluxo de entrada e os campos da fonte de referência. Primeiro é preciso criar uma nova fonte na tela de design de fluxo de dados para ser usada como o lado direito da pesquisa.

Quando correspondências forem encontradas, as linhas e colunas resultantes da fonte de referência serão adicionadas ao seu fluxo de dados. Você pode escolher que campos de interesse deseja incluir em seu coletor no final do fluxo de dados.

## <a name="match--no-match"></a>Correspondência/nenhuma correspondência

Após a transformação pesquisa, você pode usar as transformações subsequentes para inspecionar os resultados de cada linha correspondente usando a função de expressão `isMatch()` para fazer mais escolhas em sua lógica com base em se a pesquisa resultou ou não em uma correspondência de linha ou não.

## <a name="optimizations"></a>Otimizações

No Data Factory, os fluxos de dados são executados em ambientes Spark expandidos. Se o conjunto de seus conjuntos de trabalho puder se ajustar ao espaço de memória do nó do trabalhador, podemos otimizar seu desempenho de pesquisa.

![](media/data-flow/broadcast.png "Junção de difusão") de junção de difusão

### <a name="broadcast-join"></a>Junção de transmissão

Selecione junção de difusão esquerda e/ou direita para solicitar que o ADF envie por push todo o conjunto de um dos lados da relação de pesquisa para a memória.

### <a name="data-partitioning"></a>Particionamento de dados

Você também pode especificar o particionamento de seus dados selecionando "definir particionamento" na guia otimizar da transformação pesquisa para criar conjuntos de dados que podem se ajustar melhor à memória por trabalho.

## <a name="next-steps"></a>Próximas etapas

As transformações [Join](data-flow-join.md) e [Exists](data-flow-exists.md) executam tarefas semelhantes em fluxos de dados de mapeamento do ADF. Observe essas transformações em seguida.
