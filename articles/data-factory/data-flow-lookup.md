---
title: Mapeando a transformação pesquisa de fluxo de dados
description: Transformação de pesquisa de fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2020
ms.openlocfilehash: 2216e1bf058eef486dbfefba24d52bdc6bdb232f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164671"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformação de pesquisa de fluxo de dados de mapeamento de Azure Data Factory

Use a pesquisa para adicionar dados de referência de outra fonte a seu fluxo de dados. A transformação de pesquisa requer a definição de uma fonte que aponte para a sua tabela de referência e apresente correspondência com os campos de chave.

![Transformação Pesquisa](media/data-flow/lookup1.png "Pesquisa")

Selecione os campos de chave que você deseja comparar entre os campos de fluxo de entrada e os campos da fonte de referência. Primeiro é preciso criar uma nova fonte na tela de design de fluxo de dados para ser usada como o lado direito da pesquisa.

Quando correspondências forem encontradas, as linhas e colunas resultantes da fonte de referência serão adicionadas ao seu fluxo de dados. Você pode escolher que campos de interesse deseja incluir em seu coletor no final do fluxo de dados. Como alternativa, use uma transformação SELECT após sua pesquisa para remover a lista de campos para manter somente os campos de ambos os fluxos que você gostaria de reter.

A transformação pesquisa executa o equivalente a uma junção externa esquerda. Portanto, você verá que todas as linhas de sua fonte esquerda se combinam com correspondências do lado direito. Se você tiver vários valores correspondentes em sua pesquisa, ou se quiser personalizar a expressão de pesquisa, é preferível alternar para uma transformação de junção e usar uma junção cruzada. Isso evitará possíveis erros de produtos cartesianos na execução.

## <a name="match--no-match"></a>Correspondência/nenhuma correspondência

Após a transformação pesquisa, você pode usar as transformações subsequentes para inspecionar os resultados de cada linha correspondente usando a função Expression `isMatch()` para fazer mais escolhas em sua lógica com base em se a pesquisa resultou ou não em uma correspondência de linha ou não.

![Padrão de pesquisa](media/data-flow/lookup111.png "Padrão de pesquisa")

Depois de usar a transformação pesquisa, você pode adicionar uma divisão de transformação de divisão condicional na função ```isMatch()```. No exemplo acima, as linhas correspondentes passam pelo fluxo superior e as linhas não correspondentes fluem por meio do fluxo de ```NoMatch```.

## <a name="first-or-last-value"></a>Primeiro ou último valor

A transformação pesquisa é implementada como uma junção externa esquerda. Quando você tem várias correspondências de sua pesquisa, convém reduzir as várias linhas correspondentes selecionando a primeira linha correspondente, a última correspondência ou qualquer linha aleatória.

### <a name="option-1"></a>Opção 1

![Pesquisa de linha única](media/data-flow/singlerowlookup.png "Pesquisa de linha única")

* Corresponder várias linhas: deixe em branco para retornar a correspondência de linha única
* Corresponder em: selecione primeira, última ou qualquer correspondência
* Condições de classificação: se você selecionar primeiro ou último, o ADF exigirá que seus dados sejam ordenados para que haja lógica por trás da primeira e da última

> [!NOTE]
> Use apenas a primeira ou a última opção no seletor de linha simples se você precisar controlar qual valor deve ser retornado de sua pesquisa. O uso de "any" ou de pesquisas de várias linhas será executado mais rapidamente.

### <a name="option-2"></a>Opção 2

Você também pode fazer isso usando uma transformação Agregação após a pesquisa. Nesse caso, uma transformação agregada chamada ```PickFirst``` é usada para escolher o primeiro valor das correspondências de pesquisa.

![Agregação de pesquisa](media/data-flow/lookup333.png "Agregação de pesquisa")

![Pesquisar primeiro](media/data-flow/lookup444.png "Pesquisar primeiro")

## <a name="optimizations"></a>Otimizações

No Data Factory, os fluxos de dados são executados em ambientes Spark expandidos. Se o conjunto de seus conjuntos de trabalho puder se ajustar ao espaço de memória do nó do trabalhador, podemos otimizar seu desempenho de pesquisa.

![Junção de difusão](media/data-flow/broadcast.png "Junção de difusão")

### <a name="broadcast-join"></a>Junção de transmissão

Selecione junção de difusão esquerda e/ou direita para solicitar que o ADF envie por push todo o conjunto de um dos lados da relação de pesquisa para a memória. Para conjuntos de tabelas menores, isso pode melhorar muito o desempenho da pesquisa.

### <a name="data-partitioning"></a>Particionamento de dados

Você também pode especificar o particionamento de seus dados selecionando "definir particionamento" na guia otimizar da transformação pesquisa para criar conjuntos de dados que podem se ajustar melhor à memória por trabalho.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* As transformações [Join](data-flow-join.md) e [Exists](data-flow-exists.md) executam tarefas semelhantes em fluxos de dados de mapeamento do ADF. Observe essas transformações em seguida.
* Usar uma [divisão condicional](data-flow-conditional-split.md) com ```isMatch()``` para dividir linhas em valores correspondentes e não correspondentes
