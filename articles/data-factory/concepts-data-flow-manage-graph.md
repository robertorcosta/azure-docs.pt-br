---
title: Gráficos de fluxo de dados
description: Como trabalhar com gráficos de fluxo de dados de fábrica de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928318"
---
# <a name="mapping-data-flow-graphs"></a>Mapeando gráficos de fluxo de dados

A superfície de projeto de fluxos de dados de mapeamento é uma superfície de "construção" onde você constrói fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo de adição (+). Concentre-se na lógica de negócios em vez de conectar nós por meio de bordas em um ambiente de DAG de forma livre.

Abaixo estão mecanismos incorporados para gerenciar o gráfico de fluxo de dados.

## <a name="move-nodes"></a>Mover nós

![Opções de Transformação Agregada](media/data-flow/agghead.png "cabeçalho agregador")

Sem um paradigma de arrastar e soltar, a maneira de "mover" um nó de transformação é mudar o fluxo de entrada. Em vez disso, você moverá as transformações alterando o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No Fluxo de Dados do Azure Data Factory, os fluxos declaram o fluxo de dados. No painel de configurações de transformação, você verá um campo "Fluxo de Entrada". Isso informa qual fluxo de dados de entrada está alimentando a transformação. Você pode alterar a localização física do seu nó de transformação no gráfico clicando no nome do fluxo de entrada e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes no fluxo, será movida para a nova localização.

Se você estiver movendo uma transformação com uma ou mais transformações depois dela, a nova localização no fluxo de dados será adicionado por meio de um novo branch.

Se não tiver transformações subsequentes após o nó que você selecionou, somente essa transformação será movida para a nova localização.

## <a name="hide-graph-and-show-graph"></a>Ocultar gráfico e mostrar gráfico

Há um botão na extrema direita do painel de configuração inferior onde você pode expandir o painel inferior para a tela cheia ao trabalhar em configurações de transformação. Isso permitirá que você use botões "anteriores" e "próximos" para navegar pelas configurações do gráfico. Para voltar à exibição do gráfico, clique no botão para baixo e retorne à tela dividida.

## <a name="search-graph"></a>Gráfico de pesquisa

Você pode pesquisar o gráfico com o botão de pesquisa na superfície do projeto.

![Pesquisar](media/data-flow/search001.png "Gráfico de pesquisa")

## <a name="next-steps"></a>Próximas etapas

Depois de concluir o design do Fluxo de Dados, ligue o botão de depuração e teste-o no modo de depuração diretamente no [designer de fluxo](concepts-data-flow-debug-mode.md) de dados ou na [depuração do pipeline](control-flow-execute-data-flow-activity.md).
