---
title: Construtor de expressão no mapeamento do fluxo de dados
description: Construa expressões usando o Expression Builder no mapeamento de fluxos de dados na Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c28305cc55fcc561863670a15480782ce63e75b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415508"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Construa expressões no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

No mapeamento do fluxo de dados, muitas propriedades de transformação são inseridas como expressões. Essas expressões são compostas de valores de coluna, parâmetros, funções, operadores e literais que avaliam um tipo de dados Spark no tempo de execução.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Construtor de expressão aberto

A interface de edição de expressão na experiência do usuário da Fábrica de Dados do Azure é conhecida como Expression Builder. À medida que você insere sua lógica de expressão, a Fábrica de Dados usa a conclusão do código [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) para destacar, verificar sintaxe e completar automaticamente.

![Construtor de expressões](media/data-flow/xpb1.png "Construtor de Expressões")

Em transformações como a coluna derivada e o filtro, onde as expressões são obrigatórias, abra o Expression Builder selecionando a caixa de expressão azul.

![Caixa de expressão azul](media/data-flow/expressionbox.png "Construtor de Expressões")

Quando você faz referência a colunas em uma condição correspondente ou em grupo, uma expressão pode extrair valores de colunas. Para criar uma expressão, **selecione Coluna Computada**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Construtor de Expressões")

Nos casos em que uma expressão ou um valor literal são entradas válidas, selecione **Adicionar conteúdo dinâmico** para construir uma expressão que avalie para um valor literal.

![Adicionar opção de conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Construtor de Expressões")

## <a name="expression-language-reference"></a>Referência da linguagem de expressão

O mapeamento de fluxos de dados tem funções incorporadas e operadores que podem ser usados em expressões. Para obter uma lista de funções disponíveis, consulte [Funções expression no fluxo de dados de mapeamento](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nomes de colunas com caracteres especiais

Quando você tiver nomes de colunaque incluem caracteres ou espaços especiais, cerque o nome com chaves para referenciar-los em uma expressão.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Resultados de expressão de visualização

Se o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ligado, você poderá usar o cluster Spark ao vivo para ver uma visualização em andamento do que sua expressão avalia. À medida que você constrói sua lógica, você pode depurar sua expressão em tempo real. 

![Pré-visualização em andamento](media/data-flow/exp4b.png "Visualização de dados de expressão")

Selecione **Atualizar** para atualizar os resultados de sua expressão contra uma amostra ao vivo de sua fonte.

![Botão Atualizar](media/data-flow/exp5.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cadeias de caracteres

Use aspas para acompanhar o texto de seqüência literal junto com as expressões. Você pode incluir funções de expressão, colunas e parâmetros. A interpolação de strings é útil para evitar o uso extensivo da concatenação de strings quando os parâmetros são incluídos nas strings de consulta. Para usar a sintaxe de expressão, atenha-a em aparelhos encaracolados,

Alguns exemplos de interpolação de cordas:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Expressões de comentários

Adicione comentários às suas expressões usando sintaxe de comentários de linha única e multilinha.

![Sintaxe de comentários uniline e multilinha](media/data-flow/comments.png "Comentários")

Os exemplos a seguir são comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se você colocar um comentário no topo da sua expressão, ele aparecerá na caixa de texto de transformação para documentar suas expressões de transformação.

![Comente na caixa de texto de transformação](media/data-flow/comments2.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções de linguagem de expressão usam sintaxe de expressão regular. Quando você usa funções de expressão regulares,\\o Expression Builder tenta interpretar uma barra invertida () como uma seqüência de caracteres de fuga. Quando você usar barras invertidas em sua expressão regular, utilize\`todo o regex em backticks () ou use uma barra traseira dupla.

Um exemplo que usa backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Um exemplo que usa barras duplas:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Índices de matriz de endereços

Com funções de expressão que retornam matrizes, use suportes ([]) para endereçar índices específicos dentro desse objeto de matriz de retorno. A matriz é baseada em ones.

![Matriz de Construtor de Expressões](media/data-flow/expb2.png "Visualização de dados de expressão")

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

* Ctrl+K Ctrl+C: Comente toda a linha.
* Ctrl+K Ctrl+U: Uncomment.
* F1: Forneça comandos de ajuda ao editor.
* Tecla alt+down: Mova para baixo a linha atual.
* Tecla alt+up: Mova a linha atual.
* CTRL+Spacebar: Mostrar ajuda no contexto.

## <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou carimbos de datas

Para incluir literais de seqüência na ```toString()```sua saída de carimbo de tempo, enrole sua conversão em .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos da época em uma `toTimestamp(<number of milliseconds>)`data ou carimbo de data, use . Se o tempo chegar em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" de arrasto no final da expressão anterior significa conversão para um tipo longo como sintaxe inline.

## <a name="next-steps"></a>Próximas etapas

[Comece a construir expressões de transformação de dados](data-flow-expression-functions.md)
