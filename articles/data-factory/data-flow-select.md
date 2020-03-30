---
title: Mapeamento do fluxo de dados Seleção de Transformação
description: Azure Data Factory mapeando fluxo de dados Select Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: cfa15f5424dcd5d52b03fb65afe051444127f5ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065336"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selecione a transformação no mapeamento do fluxo de dados

Use a transformação selecionada para renomear, soltar ou reordenar colunas. Essa transformação não altera os dados da linha, mas escolhe quais colunas são propagadas a jusante. 

Em uma transformação selecionada, os usuários podem especificar mapeamentos fixos, usar padrões para fazer mapeamento baseado em regras ou ativar mapeamento automático. Mapeamentos fixos e baseados em regras podem ser usados na mesma transformação selecionada. Se uma coluna não corresponder a um dos mapeamentos definidos, ela será descartada.

## <a name="fixed-mapping"></a>Mapeamento fixo

Se houver menos de 50 colunas definidas em sua projeção, todas as colunas definidas terão um mapeamento fixo por padrão. Um mapeamento fixo pega uma coluna definida e de entrada e mapeia um nome exato.

![Mapeamento fixo](media/data-flow/fixedmapping.png "Mapeamento fixo")

> [!NOTE]
> Você não pode mapear ou renomear uma coluna à deriva usando um mapeamento fixo

### <a name="mapping-hierarchical-columns"></a>Mapeamento de colunas hierárquicas

Mapeamentos fixos podem ser usados para mapear uma subcoluna de uma coluna hierárquica para uma coluna de nível superior. Se você tiver uma hierarquia definida, use a coluna parada para selecionar uma subcoluna. A transformação selecionada criará uma nova coluna com o valor e o tipo de dados da subcoluna.

![mapeamento hierárquico](media/data-flow/select-hierarchy.png "mapeamento hierárquico")

## <a name="rule-based-mapping"></a>mapeamento baseado em regras

Se você deseja mapear muitas colunas de uma só vez ou passar colunas à deriva rio abaixo, use mapeamento baseado em regras para definir seus mapeamentos usando padrões de coluna. Coincidir com `name`base `type` `stream`nas `position` colunas e nas colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão padrão para um mapeamento baseado em regras que corresponda a cada coluna e produz o nome inserido. 

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

Se você tiver várias correspondências de regex no `$n` nome da coluna, você pode se referir a partidas específicas usando onde 'n' refere-se a qual correspondência. Por exemplo, '$2' refere-se à segunda correspondência dentro de um nome de coluna.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se sua projeção definida tiver uma hierarquia, você pode usar o mapeamento baseado em regras para mapear as subcolunas de hierarquias. Especifique uma condição de correspondência e a coluna complexa cujas subcolunas você deseja mapear. Cada subcoluna combinada será descontinuada usando a regra 'Nome as' especificada à direita.

![mapeamento baseado em regras](media/data-flow/rule-based-hierarchy.png "mapeamento baseado em regras")

O exemplo acima corresponde a todas `a`as subcolunas de coluna complexa . `a`contém duas subcolunas `b` e `c`. O esquema de saída incluirá `b` duas `c` colunas e como `$$`a condição 'Nome as' é .

### <a name="parameterization"></a>Parametrização

Você pode parametrizar nomes de colunas usando mapeamento baseado em regras. Use a ```name``` palavra-chave para combinar nomes de coluna saem com um parâmetro. Por exemplo, se você tiver ```mycolumn```um parâmetro de fluxo de dados, você ```mycolumn```pode criar uma regra que corresponda a qualquer nome de coluna que seja igual a . Você pode renomear a coluna combinada para uma seqüência de string codificada como 'chave de negócios' e referenciar-a explicitamente. Neste exemplo, a condição ```name == $mycolumn``` de correspondência é e a condição do nome é 'chave de negócios'. 

## <a name="auto-mapping"></a>Mapeamento automático

Ao adicionar uma transformação selecionada, **o mapeamento automático** pode ser ativado com utande do controle deslizante de mapeamento automático. Com o mapeamento automático, a transformação selecionada mapeia todas as colunas recebidas, excluindo duplicatas, com o mesmo nome de sua entrada. Isso incluirá colunas à deriva, o que significa que os dados de saída podem conter colunas não definidas em seu esquema. Para obter mais informações sobre colunas à deriva, consulte [o esquema drift](concepts-data-flow-schema-drift.md).

![Mapeamento automático](media/data-flow/automap.png "Mapeamento automático")

Com o mapeamento automático ligado, a transformação selecionada honrará as configurações duplicadas de pular e fornecerá um novo alias para as colunas existentes. O aliasing é útil ao fazer várias participações ou maquinas no mesmo fluxo e em cenários de auto-adesão. 

## <a name="duplicate-columns"></a>Colunas duplicadas

Por padrão, a transformação selecionada derruba colunas duplicadas tanto na projeção de entrada quanto de saída. Colunas de entrada duplicadas geralmente vêm de transformações de adesão e de aparência onde os nomes das colunas são duplicados em cada lado da parte. Colunas de saída duplicadas podem ocorrer se você mapear duas colunas de entrada diferentes para o mesmo nome. Escolha se deve soltar ou passar em colunas duplicadas, alternando a caixa de seleção.

![Pular duplicatas](media/data-flow/select-skip-dup.png "Pular duplicatas")

## <a name="ordering-of-columns"></a>Encomenda de colunas

A ordem dos mapeamentos determina a ordem das colunas de saída. Se uma coluna de entrada for mapeada várias vezes, apenas o primeiro mapeamento será honrado. Para qualquer coluna duplicada caindo, a primeira partida será mantida.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Exemplo

Abaixo está um exemplo de um mapeamento selecionado e seu script de fluxo de dados:

![Selecione o exemplo do script](media/data-flow/select-script-example.png "Selecione o exemplo do script")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Próximas etapas
* Depois de usar Select para renomear, reordenar e alias colunas, use a [transformação Sink](data-flow-sink.md) para pousar seus dados em um armazenamento de dados.
