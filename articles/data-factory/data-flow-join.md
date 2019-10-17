---
title: Transformação de junção do Fluxo de Dados do Azure Data Factory
description: Transformação de junção do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387233"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapeando a transformação junção de fluxo de dados



Use a junção para combinar dados de duas tabelas em seu Fluxo de Dados. Clique na transformação que será a relação esquerda e adicione uma transformação de junção da caixa de ferramentas. Dentro da transformação de junção, você selecionará outro fluxo de dados de seu fluxo de dados para ser relação direita.

![Transformação de junção](media/data-flow/join.png "Ingressar")

## <a name="join-types"></a>Tipos de junção

A seleção do tipo de junção é necessária para a transformação junção.

### <a name="inner-join"></a>Junção interna

A junção interna passará apenas por linhas que correspondam às condições de coluna de ambas as tabelas.

### <a name="left-outer"></a>Externa esquerda

Todas as linhas do fluxo esquerdo que não atenderem à condição de junção são passadas e as colunas de saída da outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="right-outer"></a>Externa direita

Todas as linhas do fluxo direito que não atenderem à condição de junção são passadas e as colunas de saída que correspondem à outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="full-outer"></a>Externa completa

Externo completo produz todas as colunas e linhas de ambos os lados com valores nulos para colunas que não estão presentes na outra tabela.

### <a name="cross-join"></a>União cruzada

Especifique o produto cruzado dos dois fluxos com uma expressão. Você pode usar isso para criar condições de junção personalizadas.

## <a name="specify-join-conditions"></a>Especificar condições de junção

A condição Junção Esquerda é decorrente do fluxo de dados conectado à esquerda da sua junção. A condição Junção Direita é o segundo fluxo de dados conectado à sua junção na parte inferior, que será um conector direto para outro fluxo ou para uma referência a outro fluxo.

É necessário inserir pelo menos 1 (1..n) condições de junção. Elas podem ser campos referenciados diretamente, selecionados do menu suspenso, ou expressões.

## <a name="join-performance-optimizations"></a>Otimizações de desempenho de junção

Ao contrário da Junção de Mesclagem em ferramentas como o SSIS, a Junção no Fluxo de Dados do ADF não é uma operação de junção de mesclagem obrigatória. Portanto, as chaves de junção não precisam ser classificadas primeiro. A operação de junção ocorrerá com base na operação de junção ideal no Spark: difusão/junção no lado do mapa:

![Transformação de junção otimizar](media/data-flow/joinoptimize.png "Otimização de junção")

Se o conjunto de seus conjuntos de trabalho puder se ajustar à memória do nó do trabalhador, podemos otimizar seu desempenho de junção. Também é possível especificar o particionamento dos dados na operação de junção para criar conjuntos de dados que podem se adequar melhor à memória por trabalho.

## <a name="self-join"></a>Autojunção

É possível obter as condições de autojunção no Fluxo de Dados do ADF usando a transformação de seleção para atribuir um alias a um fluxo existente. Primeiro, crie um “Novo branch” em um fluxo; em seguida, adicione um Selecionar para atribuir um alias ao fluxo original inteiro.

![Auto-associação](media/data-flow/selfjoin.png "Auto-associação")

No diagrama acima, a Transformação de seleção está na parte superior. Tudo o que ela está fazendo é atribuir um alias ao fluxo original como “OrigSourceBatting”. Na transformação de junção realçada embaixo dele, é possível ver que usamos esse fluxo de alias de Junção como a junção direita, permitindo-nos referenciar a mesma chave no lado esquerdo e no direito da junção interna.

## <a name="composite-and-custom-keys"></a>Chaves compostas e personalizadas

Você pode criar chaves compostas e personalizadas em tempo real dentro da transformação de junção. Adicione linhas para colunas de junção adicionais com o sinal de adição (+) ao lado de cada linha de relação. Ou Compute um novo valor de chave no construtor de expressões para um valor de junção em tempo real.

## <a name="next-steps"></a>Próximos passos

Depois de unir os dados, você pode [criar novas colunas](data-flow-derived-column.md) e [coletar seus dados para um armazenamento de dados de destino](data-flow-sink.md).
