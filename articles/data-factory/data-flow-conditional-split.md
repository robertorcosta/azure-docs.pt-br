---
title: Transformação Divisão condicional do fluxo de dados de mapeamento de Azure Data Factory
description: Transformação de Divisão Condicional do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387899"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapeando a transformação de divisão condicional do fluxo de dados



![caixa de ferramentas de divisão condicional](media/data-flow/conditionalsplit2.png "caixa de ferramentas de divisão condicional")

A transformação de divisão condicional pode rotear linhas de dados para diferentes fluxos dependendo do conteúdo dos dados. A implementação da transformação de divisão condicional é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado. Essa transformação também fornece uma saída padrão, para que se uma linha não corresponder a nenhuma expressão ela seja direcionada para a saída padrão.

![Divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="multiple-paths"></a>Vários caminhos

Para adicionar outras condições, selecione "Adicionar Stream" no painel de configuração inferior e clique na caixa de texto do construtor de expressões para criar sua expressão.

![Divisão condicional múltipla](media/data-flow/conditionalsplit3.png "Divisão condicional múltipla")

## <a name="next-steps"></a>Próximos passos

Transformações de fluxo de dados comuns usadas com divisão condicional: [Associação de junção](data-flow-join.md), [transformação pesquisa](data-flow-lookup.md), [seleção de transformação](data-flow-select.md)
