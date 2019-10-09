---
title: Construtor de Expressões de Fluxo de Dados de Mapeamento do Azure Data Factory
description: O Construtor de Expressões para Fluxos de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030298"
---
# <a name="mapping-data-flow-expression-builder"></a>Construtor de Expressões de Fluxo de Dados de Mapeamento



No Fluxo de Dados de Mapeamento do Azure Data Factory, você encontrará caixas de expressões nas quais será possível inserir expressões para transformação de dados. Use colunas, campos, variáveis, parâmetros, funções do fluxo de dados nessas caixas. Para construir a expressão, use o Construtor de Expressões que é iniciado clicando na caixa de texto de expressões dentro da transformação. Às vezes, você também verá opções de "Coluna Computada" ao selecionar colunas para transformação. Ao clicar nessa opção, você também verá a inicialização do Construtor de Expressões.

![Construtor de Expressões](media/data-flow/xpb1.png "Construtor de Expressões")

A ferramenta Construtor de Expressões usa como padrão a opção do editor de texto. o recurso de preenchimento automático faz leitura de todo o modelo de objeto do Fluxo de Dados do Azure Data Factory com verificação de sintaxe e realce.

![Preenchimento automático do Construtor de Expressões](media/data-flow/expb1.png "Preenchimento automático do Construtor de Expressões")

## <a name="build-schemas-in-output-schema-pane"></a>Criar esquemas no painel de esquema de saída

![Adicionar](media/data-flow/complexcolumn.png "colunas de adição") de coluna complexa

No painel esquema de saída à esquerda, você verá as colunas que está modificando e adicionando ao seu esquema. Você pode criar interativamente estruturas de dados simples e complexas aqui. Adicione campos adicionais usando "adicionar coluna" e crie hierarquias usando "Adicionar subcoluna".

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

## <a name="data-preview-in-debug-mode"></a>Visualização de dados no modo de depuração

![Construtor de Expressões](media/data-flow/exp4b.png "Visualização dos Dados de Expressão")

Quando você estiver trabalhando em suas expressões de fluxo de dados, ative o modo de depuração da superfície de design Azure Data Factory fluxo de dados, habilitando a visualização em andamento ao vivo dos resultados de seus dados da expressão que você está criando. A depuração dinâmica em tempo real está habilitada para as expressões.

![Modo de Depuração](media/data-flow/debugbutton.png "Botão Depurar")

Clique no botão atualizar para atualizar os resultados da expressão em um exemplo ao vivo de sua fonte em tempo real.

![Construtor de Expressões](media/data-flow/exp5.png "Visualização dos Dados de Expressão")

## <a name="comments"></a>Comentários

Adicione comentários às expressões usando sintaxe de comentário de linha única e multilinhas:

![Comentários](media/data-flow/comments.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

A linguagem de expressão do Fluxo de Dados do Azure Data Factory, [documentação de referência completa aqui](https://aka.ms/dataflowexpressions), habilita funções que incluem sintaxe de expressão regular. Ao usar funções de expressão regular, o construtor de expressões tentará interpretar a barra invertida (\\) como uma sequência de caracteres de escape. Ao usar barras invertidas em sua expressão regular, coloque todo o Regex em tiques (\`) ou use uma barra invertida dupla.

Exemplo usando tiques

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ou usando barra dupla

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Endereçando índices de matriz

Com funções de expressão que retornam matrizes, use colchetes [] para endereçar índices específicos dentro desse objeto de matriz de retorno. A matriz baseia-se neles.

![Matriz do Construtor de Expressões](media/data-flow/expb2.png "Visualização dos Dados de Expressão")

## <a name="handling-names-with-special-characters"></a>Manipulando nomes com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Próximas etapas

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
