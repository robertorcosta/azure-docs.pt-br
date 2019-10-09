---
title: Transformação de Divisão Condicional do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Divisão Condicional do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026926"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapeando a transformação de divisão condicional do fluxo de dados



(media/data-flow/conditionalsplit2.png "caixa") de ferramentas divisão condicional da ![caixa de ferramentas de divisão condicional]

A transformação de divisão condicional pode rotear linhas de dados para diferentes fluxos dependendo do conteúdo dos dados. A implementação da transformação de divisão condicional é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado. Essa transformação também fornece uma saída padrão, para que se uma linha não corresponder a nenhuma expressão ela seja direcionada para a saída padrão.

(media/data-flow/conditionalsplit1.png "Opções de divisão condicional") de ![divisão condicional]

## <a name="multiple-paths"></a>Vários caminhos

Para adicionar outras condições, selecione "Adicionar Stream" no painel de configuração inferior e clique na caixa de texto do construtor de expressões para criar sua expressão.

Divisão condicional ![múltipla](media/data-flow/conditionalsplit3.png "multicondicional") Split

## <a name="next-steps"></a>Próximas etapas

Transformações de fluxo de dados comuns usadas com divisão condicional: [Transformação de junção](data-flow-join.md), [transformação pesquisa](data-flow-lookup.md), [selecionar transformação](data-flow-select.md)
