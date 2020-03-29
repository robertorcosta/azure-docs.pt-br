---
title: Parametrizando os fluxos de dados de mapeamento
description: Saiba como parametrizar um fluxo de dados de mapeamento de pipelines de fábrica de dados
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760208"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizando os fluxos de dados de mapeamento

O mapeamento dos fluxos de dados na Fábrica de Dados do Azure suporta o uso de parâmetros. Você pode definir parâmetros dentro da definição de fluxo de dados, que você pode usar ao longo de suas expressões. Os valores dos parâmetros podem ser definidos pelo pipeline de chamada através da atividade Executar fluxo de dados. Você tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

* Use a linguagem de expressão de fluxo de controle de pipeline para definir um valor dinâmico
* Use a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Use esse recurso para tornar seus fluxos de dados de uso geral, flexíveis e reutilizáveis. Você pode parametrizar as configurações e expressões do fluxo de dados com esses parâmetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Criar parâmetros em um fluxo de dados de mapeamento

Para adicionar parâmetros ao seu fluxo de dados, clique na parte em branco da tela de fluxo de dados para ver as propriedades gerais. No painel de configurações, você verá uma guia chamada **Parâmetro**. Selecione **Novo** para gerar um novo parâmetro. Para cada parâmetro, você deve atribuir um nome, selecionar um tipo e, opcionalmente, definir um valor padrão.

![Criar parâmetros de fluxo de dados](media/data-flow/create-params.png "Criar parâmetros de fluxo de dados")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Use parâmetros em um fluxo de dados de mapeamento 

Os parâmetros podem ser referenciados em qualquer expressão de fluxo de dados. Os parâmetros começam com $ e são imutáveis. Você encontrará a lista de parâmetros disponíveis dentro do Expression Builder na guia **Parâmetros.**

![Expressão do parâmetro de fluxo de dados](media/data-flow/parameter-expression.png "Expressão do parâmetro de fluxo de dados")

Você pode adicionar rapidamente parâmetros adicionais selecionando **Novo parâmetro** e especificando o nome e o tipo.

![Expressão do parâmetro de fluxo de dados](media/data-flow/new-parameter-expression.png "Expressão do parâmetro de fluxo de dados")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando em um nome de coluna como um parâmetro

Um padrão comum é passar em um nome de coluna como um valor de parâmetro. Para referenciar a coluna associada ao `byName()` parâmetro, use a função. Lembre-se de lançar a coluna para o `toString()`seu tipo apropriado com uma função de fundição como .

Por exemplo, se você quiser mapear uma coluna `columnName`de strings com base em `toString(byName($columnName))`um parâmetro, você pode adicionar uma transformação de coluna derivada igual a .

![Passando em um nome de coluna como um parâmetro](media/data-flow/parameterize-column-name.png "Passando em um nome de coluna como um paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetro de um gasoduto

Depois de criar seu fluxo de dados com parâmetros, você pode executá-lo a partir de um pipeline com a Atividade de Fluxo de Dados executar. Depois de adicionar a atividade à tela do pipeline, você será apresentado com os parâmetros de fluxo de dados disponíveis na guia **Parâmetros** da atividade.

![Definindo um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "Definindo um parâmetro de fluxo de dados")

Se o seu tipo de dados de parâmetro for string, quando você clicar na caixa de texto para definir valores de parâmetro, você pode optar por inserir um pipeline ou uma expressão de fluxo de dados. Se você escolher a expressão do pipeline, você será apresentado com o painel de expressão do pipeline. Certifique-se de incluir funções de pipeline `'@{<expression>}'`dentro da sintaxe de interpolação de seqüência usando, por exemplo:

```'@{pipeline().RunId}'```

Se o seu parâmetro não for de string tipo, você sempre será apresentado ao Data Flow Expression Builder. Aqui, você pode inserir qualquer expressão ou valores literais que desejar que corresponda ao tipo de dados do parâmetro. Abaixo estão exemplos de expressão de fluxo de dados e uma seqüência literal do construtor de expressão:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de pipeline e fluxo de dados. 

![Amostra de parâmetros de fluxo de dados](media/data-flow/parameter-example.png "Amostra de parâmetros de fluxo de dados")



## <a name="next-steps"></a>Próximas etapas
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
