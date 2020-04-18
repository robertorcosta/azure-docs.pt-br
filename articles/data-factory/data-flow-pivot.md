---
title: Mapeamento da transformação pivô do fluxo de dados
description: Dados pivôs de linhas para colunas usando o Azure Data Factory mapeando o fluxo de dados Transformação pivô
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606372"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação pivô da fábrica de dados do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use Dinamizar no Fluxo de Dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Basicamente, você pode dinamizar os valores de linha em novas colunas (transformar dados em metadados).

![Opções de pivô](media/data-flow/pivot1.png "pivô 1")

## <a name="group-by"></a>Agrupar por

![Opções de pivô](media/data-flow/pivot2.png "pivô 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de tabela dinâmica. Você pode definir mais de uma coluna aqui com o sinal + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave pivô

![Opções de pivô](media/data-flow/pivot3.png "pivô 3")

A Chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar para valores de coluna. Esta é a coluna que determinará as novas colunas que serão criadas.

## <a name="pivoted-columns"></a>Colunas pivotadas

![Opções de pivô](media/data-flow/pivot4.png "pivô 4")

Por fim, você escolherá a agregação que deseja usar para os valores dinamizados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, "Vendas" por "Região" resultaria em novos valores de coluna de cada valor de venda, ou seja, "25", "50", "1000", etc. No entanto, se você definir um valor de prefixo de "Vendas", cada valor de coluna adicionaria "Vendas-" ao início do valor.

![Opções de pivô](media/data-flow/pivot5.png "pivô 5")

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinamizadas com seus valores agregados. Definir a disposição de colunas como “Lateral” alternará entre coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que você deseja usar para os valores de dinamização, clique no campo na parte inferior do painel Colunas Dinamizadas. Você entrará no Construtor de Expressões de Fluxo de Dados do ADF, no qual poderá criar uma expressão de agregação e fornecer um nome de alias descritivo a seus novos valores de agregação.

Use a Linguagem de Expressão do Fluxo de Dados do ADF para descrever as transformações da coluna dinamizada no Construtor de Expressões: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadados pivôs

A transformação pivô produzirá novos nomes de coluna suspidos com base nos seus dados de entrada. A Chave Pivô produz os valores para cada novo nome da coluna. Se você não especificar valores individuais e desejar criar nomes de colunadinâmica para cada valor único em sua Chave Dinâmica, então a ui não exibirá os metadados em Inspecionar e não haverá propagação de colunas para a transformação do Sink. Se você definir valores para a chave pivô, o ADF poderá determinar os novos nomes da coluna e os nomes das colunas estarão disponíveis para você no mapeamento Inspecionar e Afundar.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Gerar um novo modelo a partir de colunas dinâmicas

O pivô gera novos nomes de coluna dinamicamente com base nos valores da linha. Você pode transformar essas novas colunas em metadados que podem ser referenciados mais tarde no seu fluxo de dados. Para fazer isso, clique na guia Visualização de dados. Todas as novas colunas geradas pela sua transformação pivô aparecem com um ícone "à deriva" no cabeçalho da tabela. Clique no botão "Mapa à deriva" para transformar essas novas colunas em metadados, tornando-as parte do modelo do seu fluxo de dados.

![Colunas pivôs](media/data-flow/newpivot1.png "Mapa decolunas pivôs à deriva")

### <a name="landing-new-columns-in-sink"></a>Aterrissando novas colunas em Sink

Mesmo com nomes de colunas dinâmicas no Pivot, você ainda pode afundar seus novos nomes e valores de coluna em sua loja de destino. Basta definir "Permitir deriva de esquema" em suas configurações de Sink. Você não verá os novos nomes dinâmicos em seus metadados de coluna, mas a opção de deriva de esquema permitirá que você aterrisse os dados.

### <a name="view-metadata-in-design-mode"></a>Exibir metadados no modo de design

Se você deseja visualizar os novos nomes de coluna como metadados em Inspecionar e deseja ver as colunas se propagarem explicitamente para a transformação De Sink, defina valores explícitos na guia Tecla pivô.

### <a name="how-to-rejoin-original-fields"></a>Como reingressar nos campos originais
A transformação dinâmica só projetará as colunas usadas nas ações de agregação, agrupamento e dinamização. Se você deseja incluir as outras colunas da etapa anterior em seu fluxo, use um Novo Ramo da etapa anterior e use o padrão de auto-adesão para conectar o fluxo com os metadados originais.

## <a name="next-steps"></a>Próximas etapas

Tente a [transformação sem pivô](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
