---
title: Construtor de expressão de fluxo de dados de mapeamento de Azure Data Factory
description: O construtor de expressões para mapear Azure Data Factory fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388045"
---
# <a name="mapping-data-flow-expression-builder"></a>Construtor de expressões de fluxo de dados de mapeamento



No fluxo de dados de mapeamento Azure Data Factory, você encontrará caixas de expressão nas quais é possível inserir expressões para transformação de dados. Use colunas, campos, variáveis, parâmetros, funções do fluxo de dados nessas caixas. Para construir a expressão, use o Construtor de Expressões que é iniciado clicando na caixa de texto de expressões dentro da transformação. Às vezes, você também verá opções de "Coluna Computada" ao selecionar colunas para transformação. Ao clicar nessa opção, você também verá a inicialização do Construtor de Expressões.

![Construtor de expressões](media/data-flow/xpb1.png "Construtor de Expressões")

A ferramenta Construtor de Expressões usa como padrão a opção do editor de texto. o recurso de preenchimento automático faz leitura de todo o modelo de objeto do Fluxo de Dados do Azure Data Factory com verificação de sintaxe e realce.

![Preenchimento automático do construtor de expressões](media/data-flow/expb1.png "Preenchimento automático do construtor de expressões")

## <a name="build-schemas-in-output-schema-pane"></a>Criar esquemas no painel de esquema de saída

![Adicionar coluna complexa](media/data-flow/complexcolumn.png "Adicionar colunas")

No painel esquema de saída à esquerda, você verá as colunas que está modificando e adicionando ao seu esquema. Você pode criar interativamente estruturas de dados simples e complexas aqui. Adicione campos adicionais usando "adicionar coluna" e crie hierarquias usando "Adicionar subcoluna".

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

## <a name="data-preview-in-debug-mode"></a>Visualização de dados no modo de depuração

![Construtor de expressões](media/data-flow/exp4b.png "Visualização de dados de expressão")

Quando você estiver trabalhando em suas expressões de fluxo de dados, ative o modo de depuração da superfície de design Azure Data Factory fluxo de dados, habilitando a visualização em andamento ao vivo dos resultados de seus dados da expressão que você está criando. A depuração dinâmica em tempo real está habilitada para as expressões.

![Modo de depuração](media/data-flow/debugbutton.png "Botão de depuração")

Clique no botão atualizar para atualizar os resultados da expressão em um exemplo ao vivo de sua fonte em tempo real.

![Construtor de expressões](media/data-flow/exp5.png "Visualização de dados de expressão")

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

![Matriz do construtor de expressões](media/data-flow/expb2.png "Visualização de dados de expressão")

## <a name="handling-names-with-special-characters"></a>Manipulando nomes com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Próximos passos

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
