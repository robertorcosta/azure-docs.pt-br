---
title: Mapeando fluxo de dados selecionar transformação
description: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/08/2020
ms.openlocfilehash: 2d04de420f743e4fef4cff4bd2912559dae0886a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934170"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapeando fluxo de dados selecionar transformação


Use essa transformação para a seletividade da coluna (reduzindo o número de colunas), colunas de alias e nomes de fluxo e reordenar colunas.

## <a name="how-to-use-select-transformation"></a>Como usar a transformação selecionar
A Transformação de seleção permite que você crie um alias para um fluxo inteiro ou para colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente em seu fluxo de dados. Essa transformação é útil para cenários de autojunção. Uma maneira de implementar uma autojunção no Fluxo de Dados do ADF é usar um fluxo, ramificá-lo com “Novo branch” e, imediatamente depois, adicionar uma transformação “de seleção”. Esse fluxo agora terá um novo nome que pode ser usado para ingressar de volta ao fluxo original, criando uma autojunção:

![Auto-associação](media/data-flow/selfjoin.png "Auto-associação")

No diagrama acima, a Transformação de seleção está na parte superior. Isso é atribuir alias ao fluxo original como “OrigSourceBatting”. Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

A seleção também pode ser usada como uma maneira de anular a seleção de colunas do seu fluxo de dados. Por exemplo, se você tiver seis colunas definidas no seu coletor, mas desejar escolher apenas três específicas para transformar e, em seguida, fluir para o coletor, será possível selecionar apenas essas três usando a transformação de seleção.

![Selecionar transformação](media/data-flow/newselect1.png "Selecionar alias")

## <a name="options"></a>Opções
* A configuração padrão para "Seleção" é incluir todas as colunas de entrada e manter esses nomes originais. É possível atribuir um alias ao fluxo definindo o nome da transformação de seleção.
* Para atribuir alias a colunas individuais, desmarque “Selecionar tudo” e use o mapeamento de colunas na parte inferior.
* Escolha ignorar duplicatas para eliminar colunas duplicadas dos metadados de entrada ou saída.

![Ignorar duplicatas](media/data-flow/select-skip-dup.png "Ignorar duplicatas")

* Quando você opta por ignorar duplicatas, os resultados ficarão visíveis na guia inspecionar. o ADF manterá a primeira ocorrência da coluna e você verá que cada ocorrência subsequente dessa mesma coluna foi removida do fluxo.

> [!NOTE]
> Para limpar as regras de mapeamento, pressione o botão **Redefinir** .

## <a name="mapping"></a>Mapeamento
Por padrão, a transformação selecionar mapeará automaticamente todas as colunas, que passarão por todas as colunas de entrada para o mesmo nome na saída. O nome do fluxo de saída definido em selecionar configurações definirá um novo nome de alias para o fluxo. Se você mantiver a seleção definir para mapa automático, poderá alias de todo o fluxo com todas as colunas iguais.

![Selecionar regras de transformação](media/data-flow/rule2.png "Mapeamento baseado em regras")

Se você quiser criar um alias, remover, renomear ou reordenar colunas, primeiro será necessário desligar o "mapa automático". Por padrão, você verá uma regra padrão inserida para você chamada "todas as colunas de entrada". Você pode deixar essa regra em vigor se pretende sempre permitir que todas as colunas de entrada sejam mapeadas para o mesmo nome em sua saída.

No entanto, se você quiser adicionar regras personalizadas, clique em "Adicionar mapeamento". O mapeamento de campo fornecerá uma lista de nomes de coluna de entrada e saída para mapear e alias. Escolha "mapeamento baseado em regras" para criar regras de correspondência de padrões.

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao escolher o mapeamento baseado em regras, você está instruindo o ADF a avaliar sua expressão de correspondência para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Você pode adicionar qualquer combinação de mapeamentos com base em campo e em regra. Os nomes de campo são então gerados em tempo de execução pelo ADF com base nos metadados de entrada da origem. Você pode exibir os nomes dos campos gerados durante a depuração e usando o painel de visualização de dados.

Mais detalhes sobre a correspondência de padrões estão disponíveis na [documentação do padrão de coluna](concepts-data-flow-column-pattern.md).

### <a name="use-rule-based-mapping-to-parameterize-the-select-transformation"></a>Usar o mapeamento baseado em regras para parametrizar a transformação Select
Você pode parametrizar o mapeamento de campo na transformação selecionar usando o mapeamento baseado em regra. Use a palavra-chave ```name``` para verificar os nomes de coluna de entrada em um parâmetro. Por exemplo, se você tiver um parâmetro de fluxo de dados chamado ```mycolumn``` você pode criar uma única regra de transformação de seleção que sempre mapeia qualquer nome de coluna definido ```mycolumn``` como um nome de campo dessa forma:

```name == $mycolumn```

## <a name="next-steps"></a>Próximas etapas
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
