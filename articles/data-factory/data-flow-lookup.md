---
title: Transformação pesquisa no fluxo de dados de mapeamento
description: Dados de referência de outra fonte usando a transformação pesquisa no fluxo de dados de mapeamento.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: af4e33e2653aebe5d1c979aa314463e4beb7b0d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233390"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformação pesquisa no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação pesquisa para fazer referência a dados de outra fonte em um fluxo de fluxo de dados. A transformação pesquisa acrescenta colunas de dados correspondentes aos dados de origem.

Uma transformação pesquisa é semelhante a uma junção externa esquerda. Todas as linhas do fluxo primário existirão no fluxo de saída com colunas adicionais do fluxo de pesquisa. 

## <a name="configuration"></a>Configuração

![Transformação pesquisa](media/data-flow/lookup1.png "Pesquisa")

**Fluxo primário:** O fluxo de entrada de dados. Esse fluxo é equivalente ao lado esquerdo de uma junção.

**Fluxo de pesquisa:** Os dados que são acrescentados ao fluxo primário. Quais dados são adicionados é determinado pelas condições de pesquisa. Esse fluxo é equivalente ao lado direito de uma junção.

**Corresponder várias linhas:** Se habilitada, uma linha com várias correspondências no fluxo principal retornará várias linhas. Caso contrário, apenas uma única linha será retornada com base na condição ' corresponder em '.

**Corresponder em:** Visível somente se ' corresponder várias linhas ' estiver habilitado. Escolha se deseja corresponder em qualquer linha, a primeira correspondência ou a última correspondência. Qualquer linha é recomendada à medida que ela é executada mais rapidamente. Se a primeira linha ou última linha for selecionada, será necessário especificar as condições de classificação.

**Condições de pesquisa:** Escolha em quais colunas corresponder. Se a condição de igualdade for atendida, as linhas serão consideradas uma correspondência. Focalize e selecione "coluna computada" para extrair um valor usando a [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md).

A transformação pesquisa dá suporte apenas a correspondências de igualdade. Para personalizar a expressão de pesquisa para incluir outros operadores, como maior que, é recomendável usar uma [junção cruzada na transformação junção](data-flow-join.md#custom-cross-join). Uma junção cruzada evitará qualquer erro de produto cartesiano possível na execução.

Todas as colunas de ambos os fluxos são incluídas nos dados de saída. Para remover colunas duplicadas ou indesejadas, adicione uma [transformação selecionar](data-flow-select.md) após a transformação pesquisa. As colunas também podem ser descartadas ou renomeadas em uma transformação de coletor.

## <a name="analyzing-matched-rows"></a>Analisando linhas correspondentes

Após a transformação pesquisa, a função `isMatch()` pode ser usada para ver se a pesquisa correspondeu a linhas individuais.

![Padrão de pesquisa](media/data-flow/lookup111.png "Padrão de pesquisa")

Um exemplo desse padrão é usar a transformação Divisão condicional para dividir a `isMatch()` função. No exemplo acima, as linhas correspondentes passam pelo fluxo superior e as linhas não correspondentes fluem por meio do ```NoMatch``` fluxo.

## <a name="testing-lookup-conditions"></a>Testando condições de pesquisa

Ao testar a transformação pesquisa com a visualização de dados no modo de depuração, use um pequeno conjunto de dados conhecidos. Ao fazer amostragem de linhas de um grande conjunto de grandes, você não pode prever quais linhas e chaves serão lidas para teste. O resultado é não determinístico, o que significa que suas condições de junção podem não retornar nenhuma correspondência.

## <a name="broadcast-optimization"></a>Otimização de difusão

![Junção de difusão](media/data-flow/broadcast.png "Junção de difusão")

Em junções, pesquisas e a transformação Exists, se um ou ambos os fluxos de dados couberem na memória do nó de trabalho, você poderá otimizar o desempenho habilitando a **difusão**. Por padrão, o mecanismo do Spark decidirá automaticamente se deseja ou não difundir um lado. Para escolher manualmente qual lado deve ser difundido, selecione **fixo**.

Não é recomendável desabilitar a difusão por meio da opção **desligar** , a menos que suas junções estejam em erros de tempo limite.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exemplo

![Transformação pesquisa](media/data-flow/lookup-dsl-example.png "Pesquisa")

O script de fluxo de dados para a configuração de pesquisa acima está no trecho de código abaixo.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Próximas etapas

* As transformações [Join](data-flow-join.md) e [Exists](data-flow-exists.md) levam em várias entradas de fluxo
* Usar uma [transformação de divisão condicional](data-flow-conditional-split.md) com ```isMatch()``` para dividir linhas em valores correspondentes e não correspondentes
