---
title: Padrões de coluna no fluxo de dados da Fábrica de Dados do Azure
description: Crie padrões generalizados de transformação de dados usando padrões de coluna no Mapeamento de fluxos de dados da Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606127"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Várias transformações de fluxo de dados de mapeamento permitem que você faça referência a colunas de modelos com base em padrões em vez de nomes de colunas codificados. Esta correspondência é conhecida como *padrões de coluna.* Você pode definir padrões para combinar colunas com base em nome, tipo de dados, fluxo ou posição em vez de exigir nomes de campo exatos. Existem dois cenários em que os padrões de coluna são úteis:

* Se os campos de origem recebidos mudarem frequentemente, como o caso de alterar colunas em arquivos de texto ou bancos de dados NoSQL. Este cenário é conhecido como [deriva de esquema](concepts-data-flow-schema-drift.md).
* Se você deseja fazer uma operação comum em um grande grupo de colunas. Por exemplo, querer lançar todas as colunas que têm 'total' em seu nome de coluna em um duplo.

Os padrões das colunas estão atualmente disponíveis nas transformações de coluna derivada, agregadas, selecionadas e de pia.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregado

Para adicionar um padrão de coluna em uma coluna derivada ou na guia Agregados de uma transformação agregada, clique no ícone de adição à direita de uma coluna existente. Selecione **Adicionar padrão de coluna**. 

![padrões de coluna](media/data-flow/columnpattern.png "Padrões de coluna")

Use o [construtor de expressão](concepts-data-flow-expression-builder.md) para entrar na condição de correspondência. Crie uma expressão booleana que `name`corresponda a colunas baseadas no , `type` `stream`e `position` da coluna. O padrão afetará qualquer coluna, derivada ou definida, onde a condição retorna verdadeira.

As duas caixas de expressão abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Use `$$` para referenciar o valor existente do campo combinado. A caixa de expressão esquerda define o nome e a caixa de expressão direita define o valor.

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

O padrão da coluna acima corresponde a cada coluna do tipo duplo e cria uma coluna agregada por correspondência. O nome da nova coluna é o nome da coluna combinada concatenado com '_total'. O valor da nova coluna é a soma arredondada e agregada do valor duplo existente.

Para verificar se sua condição de correspondência está correta, você pode validar o esquema de saída de colunas definidas na guia **Inspecionar** ou obter um instantâneo dos dados na guia **Desestatizar dados.** 

![padrões de coluna](media/data-flow/columnpattern3.png "Padrões de coluna")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em selecionar e afundar

Ao mapear colunas na origem e selecionar transformações, você pode adicionar mapeamento fixo ou mapeamento baseado em regras. Coincidir com `name`base `type` `stream`nas `position` colunas e nas colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão padrão para um mapeamento baseado em regras que corresponda a cada coluna e produz o nome inserido. 

Para adicionar um mapeamento baseado em regras, clique **em Adicionar mapeamento** e selecione mapeamento baseado em **regras**.

![mapeamento baseado em regras](media/data-flow/rule2.png "mapeamento baseado em regras")

Cada mapeamento baseado em regras requer duas entradas: a condição em que corresponder e qual nomear cada coluna mapeada. Ambos os valores são inseridos através do construtor de [expressão](concepts-data-flow-expression-builder.md). Na caixa de expressão esquerda, insira sua condição de jogo booleano. Na caixa de expressão certa, especifique para o que a coluna combinada será mapeada.

![mapeamento baseado em regras](media/data-flow/rule-based-mapping.png "mapeamento baseado em regras")

Use `$$` sintaxe para referenciar o nome de entrada de uma coluna combinada. Usando a imagem acima como exemplo, digamos que um usuário quer corresponder em todas as colunas de string cujos nomes são menores que seis caracteres. Se uma coluna recebida `test`for `$$ + '_short'` nomeada, a `test_short`expressão renomeará a coluna . Se esse for o único mapeamento que existe, todas as colunas que não atenderem à condição serão retiradas dos dados produzidos.

Os padrões combinam com colunas derivadas e definidas. Para ver quais colunas definidas são mapeadas por uma regra, clique no ícone dos óculos ao lado da regra. Verifique sua saída usando a visualização de dados.

### <a name="regex-mapping"></a>Mapeamento regex

Se você clicar no ícone chevron para baixo, você pode especificar uma condição de mapeamento regex. Uma condição de mapeamento de regex corresponde a todos os nomes de coluna que correspondem à condição de regex especificada. Isso pode ser usado em combinação com mapeamentos padrão baseados em regras.

![mapeamento baseado em regras](media/data-flow/regex-matching.png "mapeamento baseado em regras")

O exemplo acima corresponde `(r)` ao padrão regex ou qualquer nome de coluna que contenha uma caixa inferior r. Semelhante ao mapeamento padrão baseado em regras, todas as colunas `$$` combinadas são alteradas pela condição à direita usando sintaxe.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se sua projeção definida tiver uma hierarquia, você pode usar o mapeamento baseado em regras para mapear as subcolunas de hierarquias. Especifique uma condição de correspondência e a coluna complexa cujas subcolunas você deseja mapear. Cada subcoluna combinada será descontinuada usando a regra 'Nome as' especificada à direita.

![mapeamento baseado em regras](media/data-flow/rule-based-hierarchy.png "mapeamento baseado em regras")

O exemplo acima corresponde a todas `a`as subcolunas de coluna complexa . `a`contém duas subcolunas `b` e `c`. O esquema de saída incluirá `b` duas `c` colunas e como `$$`a condição 'Nome as' é .

## <a name="pattern-matching-expression-values"></a>Valores de expressão correspondentes a padrões.

* `$$`traduz-se para o nome ou valor de cada partida no tempo de execução
* `name`representa o nome de cada coluna recebida
* `type`representa o tipo de dados de cada coluna recebida
* `stream`representa o nome associado a cada fluxo, ou transformação em seu fluxo
* `position`é a posição ordinal das colunas em seu fluxo de dados

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a linguagem de [expressão](data-flow-expression-functions.md) de fluxo de dados de mapeamento para transformações de dados
* Use padrões de coluna na [transformação](data-flow-sink.md) da pia e [selecione a transformação](data-flow-select.md) com mapeamento baseado em regras
