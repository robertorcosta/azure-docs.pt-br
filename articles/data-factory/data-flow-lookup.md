---
title: Transformação de pesquisa no mapeamento do fluxo de dados
description: Dados de referência de outra fonte usando a transformação da pesquisa no mapeamento do fluxo de dados.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240404"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformação de pesquisa no mapeamento do fluxo de dados

Use a transformação de pesquisa para referenciar dados de outra fonte em um fluxo de fluxo de dados. A transformação da pesquisa anexa colunas de dados compatíveis com seus dados de origem.

Uma transformação de aparência é semelhante a uma junta externa esquerda. Todas as linhas do fluxo primário existirão no fluxo de saída com colunas adicionais do fluxo de pesquisa. 

## <a name="configuration"></a>Configuração

![transformação Pesquisa](media/data-flow/lookup1.png "Pesquisa")

**Fluxo primário:** O fluxo de dados de entrada. Este fluxo é equivalente ao lado esquerdo de uma junta.

**Fluxo de olhada:** Os dados anexados ao fluxo primário. Quais dados são adicionados é determinado pelas condições de pesquisa. Este fluxo é equivalente ao lado direito de uma junta.

**Corresponder a várias linhas:** Se ativado, uma linha com várias correspondências no fluxo principal retornará várias linhas. Caso contrário, apenas uma única linha será devolvida com base na condição 'Match on'.

**Partida em:** Somente visível se 'Corresponder várias linhas' estiver habilitado. Escolha se combina em qualquer linha, na primeira partida ou na última partida. Qualquer linha é recomendada à medida que executa a mais rápida. Se a primeira linha ou a última linha forem selecionadas, você será obrigado a especificar condições de classificação.

**Condições de olhada:** Escolha quais colunas podem combinar. Se a condição de igualdade for cumprida, então as linhas serão consideradas uma partida. Hover e selecione 'Coluna computada' para extrair um valor usando a linguagem de [expressão de fluxo de dados](data-flow-expression-functions.md).

A transformação da procuração só suporta partidas de igualdade. Para personalizar a expressão de lookup para incluir outros operadores, como maior do que, recomenda-se usar uma [interadeada na transformação de adesão.](data-flow-join.md#custom-cross-join) Uma junta cruzada evitará possíveis erros de produto cartesiano na execução.

Todas as colunas de ambos os fluxos estão incluídas nos dados de saída. Para soltar colunas duplicadas ou indesejadas, adicione uma [transformação selecionada](data-flow-select.md) após a transformação da aparência. As colunas também podem ser descartadas ou renomeadas em uma transformação da pia.

## <a name="analyzing-matched-rows"></a>Analisando linhas combinadas

Após sua transformação de `isMatch()` busca, a função pode ser usada para ver se a aparência correspondia a linhas individuais.

![Padrão de procuração](media/data-flow/lookup111.png "Padrão de procuração")

Um exemplo desse padrão é usar a transformação `isMatch()` de divisão condicional para dividir a função. No exemplo acima, as linhas correspondentes passam pelo fluxo superior ```NoMatch``` e as linhas não correspondentes fluem através do fluxo.

## <a name="testing-lookup-conditions"></a>Testando as condições de análise

Ao testar a transformação da pesquisa com visualização de dados no modo de depuração, use um pequeno conjunto de dados conhecidos. Ao provar linhas de um grande conjunto de dados, você não pode prever quais linhas e chaves serão lidas para testes. O resultado não é determinista, o que significa que suas condições de adesão podem não retornar nenhuma partida.

## <a name="broadcast-optimization"></a>Otimização de transmissão

No Azure Data Factory, os fluxos de dados de mapeamento são executados em ambientes Spark dimensionados. Se o conjunto de dados pode se encaixar no espaço de memória do nó do trabalhador, seu desempenho de pesquisa pode ser otimizado ativando a transmissão.

![Participar do Broadcast](media/data-flow/broadcast.png "Participar do Broadcast")

A ativação da transmissão empurra todo o conjunto de dados para a memória. Para conjuntos de dados menores que contêm apenas algumas milhares de linhas, a transmissão pode melhorar muito o seu desempenho de pesquisa. Para grandes conjuntos de dados, essa opção pode levar a uma exceção fora da memória.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exemplo

![transformação Pesquisa](media/data-flow/lookup-dsl-example.png "Pesquisa")

O script de fluxo de dados para a configuração de pesquisa acima está no trecho de código abaixo.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Próximas etapas

* As [transformações de juntar](data-flow-join.md) e [existir](data-flow-exists.md) ambos tomam em múltiplas entradas de fluxo
* Use uma [transformação de divisão condicional](data-flow-conditional-split.md) com ```isMatch()``` linhas divididas em valores correspondentes e não correspondentes
