---
title: Junte-se à transformação no mapeamento do fluxo de dados
description: Combine dados de duas fontes de dados usando a transformação de adesão no fluxo de dados da Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 5c388dd2b3e4f40fbf2ed75cf3f1b8ab31aee394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606412"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Junte-se à transformação no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação de juntar para combinar dados de duas fontes ou fluxos em um fluxo de dados de mapeamento. O fluxo de saída incluirá todas as colunas de ambas as fontes combinadas com base em uma condição de adesão. 

## <a name="join-types"></a>Tipos de junção

O mapeamento de fluxos de dados atualmente suporta cinco tipos diferentes de adesão.

### <a name="inner-join"></a>Junção interna

O interior junta apenas linhas de saídas que tenham valores correspondentes em ambas as tabelas.

### <a name="left-outer"></a>Externa esquerda

A junta esquerda retorna todas as linhas do fluxo esquerdo e os registros combinados do fluxo direito. Se uma linha do fluxo esquerdo não tiver correspondência, as colunas de saída do fluxo direito serão definidas como NULL. A saída será as linhas retornadas por uma junta interna mais as linhas incomparáveis do fluxo esquerdo.

> [!NOTE]
> O motor Spark usado por fluxos de dados ocasionalmente falhará devido a possíveis produtos cartesianos em suas condições de adesão. Se isso ocorrer, você pode mudar para uma inter-junta personalizada e inserir manualmente sua condição de adesão. Isso pode resultar em um desempenho mais lento nos fluxos de dados, pois o mecanismo de execução pode precisar calcular todas as linhas de ambos os lados da relação e, em seguida, filtrar linhas.

### <a name="right-outer"></a>Externa direita

A junta externa direita retorna todas as linhas do fluxo direito e os registros combinados do fluxo esquerdo. Se uma linha do fluxo direito não tiver correspondência, as colunas de saída do fluxo esquerdo serão definidas como NULL. A saída será as linhas retornadas por uma junta interna mais as linhas incomparáveis do fluxo direito.

### <a name="full-outer"></a>Externa completa

Saídas de juntação externa completa todas as colunas e linhas de ambos os lados com valores NULL para colunas que não são compatíveis.

### <a name="custom-cross-join"></a>Adesão de cruz personalizada

A junta cruzada produz o produto cruzado dos dois fluxos com base em uma condição. Se você estiver usando uma condição que não é igualdade, especifique uma expressão personalizada como sua condição de adesão cruzada. O fluxo de saída será de todas as linhas que atendam à condição de adesão.

Você pode usar este tipo de adesão para adesões e condições não-equi. ```OR```

Se você quiser produzir explicitamente um produto cartesiano completo, use a transformação da Coluna Derivada em cada um dos dois fluxos independentes antes da adesão para criar uma chave sintética para combinar. Por exemplo, crie uma nova coluna em ```SyntheticKey``` Coluna Derivada ```1```em cada fluxo chamado e defina-a igual a . Em ```a.SyntheticKey == b.SyntheticKey``` seguida, use como sua expressão de adesão personalizada.

> [!NOTE]
> Certifique-se de incluir pelo menos uma coluna de cada lado da sua relação esquerda e direita em uma interadeada personalizada. A execução de cross se junta a valores estáticos em vez de colunas de cada lado resulta em varreduras completas de todo o conjunto de dados, fazendo com que seu fluxo de dados seja mal executado.

## <a name="configuration"></a>Configuração

1. Escolha qual fluxo de dados você está se juntando no fluxo de **dados** certo.
1. Selecione seu **tipo de Participar**
1. Escolha quais colunas-chave deseja combinar para participar da condição. Por padrão, o fluxo de dados busca igualdade entre uma coluna em cada fluxo. Para comparar através de um valor computado, paire sobre a coluna suspensa e **selecione Coluna Computada**.

![Participe da Transformação](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Otimização do desempenho de junta

Ao contrário da fusão de ferramentas como o SSIS, a transformação de adesão não é uma operação de fusão obrigatória. As chaves de adesão não requerem classificação. A operação de adesão ocorre com base na operação de adesão ideal no Spark, seja na transmissão ou no lado do mapa.

![Juntar-se transformação otimizar](media/data-flow/joinoptimize.png "Participe da Otimização")

Se um ou ambos os fluxos de dados se encaixarem na memória do nó do trabalhador, melhore ainda mais o seu desempenho, habilitando o **Broadcast** na guia otimizar. Você também pode reparticionar seus dados na operação de adesão para que ele se encaixe melhor na memória por trabalhador.

## <a name="self-join"></a>Autojunção

Para se auto-juntar a um fluxo de dados consigo mesmo, alias um fluxo existente com uma transformação seleto. Crie uma nova ramificação clicando no ícone de adição ao lado de uma transformação e selecionando **Novo ramo**. Adicione uma transformação seleto ao alias do fluxo original. Adicione uma transformação de juntar e escolha o fluxo original como o **fluxo esquerdo** e a transformação selecionada como o **fluxo direito**.

![Auto-adesão](media/data-flow/selfjoin.png "Auto-adesão")

## <a name="testing-join-conditions"></a>Testar condições de adesão

Ao testar as transformações de juntar com visualização de dados no modo de depuração, use um pequeno conjunto de dados conhecidos. Ao provar linhas de um grande conjunto de dados, você não pode prever quais linhas e chaves serão lidas para testes. O resultado não é determinista, o que significa que suas condições de adesão podem não retornar nenhuma partida.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemplo de adesão interna

O exemplo abaixo é `JoinMatchedData` uma transformação `TripData` de `TripFare`junta chamada que leva fluxo esquerdo e fluxo direito .  A condição de `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` adesão é `hack_license` `medallion`a `vendor_id`expressão `pickup_datetime` que retorna verdadeira se o , , e colunas em cada correspondência de fluxo. O `joinType` é `'inner'`. Estamos permitindo a transmissão apenas no fluxo `broadcast` esquerdo, então tem valor. `'left'`

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Junte-se ao exemplo](media/data-flow/join-script1.png "Junte-se ao exemplo")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Cruzamento personalizado juntar exemplo

O exemplo abaixo é `JoiningColumns` uma transformação `LeftStream` de `RightStream`junta chamada que leva fluxo esquerdo e fluxo direito . Essa transformação leva em dois fluxos `leftstreamcolumn` e une `rightstreamcolumn`todas as linhas onde a coluna é maior que a coluna. O `joinType` é `cross`. A transmissão não `broadcast` está `'none'`habilitada tem valor .

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Junte-se ao exemplo](media/data-flow/join-script2.png "Junte-se ao exemplo")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Próximas etapas

Depois de juntar dados, crie uma [coluna derivada](data-flow-derived-column.md) e [saque](data-flow-sink.md) seus dados em um armazenamento de dados de destino.
