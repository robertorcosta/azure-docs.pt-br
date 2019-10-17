---
title: Transformação dinâmica do fluxo de dados de mapeamento de Azure Data Factory
description: Dados dinâmicos de linhas para colunas usando Azure Data Factory mapeamento dinâmico do fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e59f0623b898fedada63e51fabbaf88d8b17f59d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387789"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação dinâmica do Azure data factory


Use Dinamizar no Fluxo de Dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Basicamente, você pode dinamizar os valores de linha em novas colunas (transformar dados em metadados).

![Opções dinâmicas](media/data-flow/pivot1.png "pivô 1")

## <a name="group-by"></a>Agrupar por

![Opções dinâmicas](media/data-flow/pivot2.png "pivô 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de tabela dinâmica. Você pode definir mais de uma coluna aqui com o sinal + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave dinâmica

![Opções dinâmicas](media/data-flow/pivot3.png "pivô 3")

A chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar para valores de coluna. Esta é a coluna que determinará as novas colunas que serão criadas.

## <a name="pivoted-columns"></a>Colunas dinâmicas

![Opções dinâmicas](media/data-flow/pivot4.png "tabela dinâmica 4")

Por fim, você escolherá a agregação que deseja usar para os valores dinamizados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, dinamizar "vendas" por "Region" resultaria em novos valores de coluna de cada valor de vendas, ou seja, "25", "50", "1000" etc. No entanto, se você definir um valor de prefixo "Sales-", cada valor de coluna adicionará "Sales" ao início do valor.

![Opções dinâmicas](media/data-flow/pivot5.png "pivô 5")

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinâmicas com seus valores agregados. Definir a disposição de colunas como “Lateral” alternará entre coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que você deseja usar para os valores de dinamização, clique no campo na parte inferior do painel Colunas Dinamizadas. Você entrará no Construtor de Expressões de Fluxo de Dados do ADF, no qual poderá criar uma expressão de agregação e fornecer um nome de alias descritivo a seus novos valores de agregação.

Use a Linguagem de Expressão do Fluxo de Dados do ADF para descrever as transformações da coluna dinamizada no Construtor de Expressões: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadados dinâmicos

A transformação dinâmica produzirá novos nomes de coluna dinâmicos com base em seus dados de entrada. A chave dinâmica produz os valores para cada novo nome de coluna. Se você não especificar valores individuais e desejar criar nomes de coluna dinâmicos para cada valor exclusivo em sua chave dinâmica, a interface do usuário não exibirá os metadados em inspeção e não haverá nenhuma propagação de coluna para a transformação do coletor. Se você definir valores para a chave dinâmica, o ADF poderá determinar os novos nomes de coluna e esses nomes de coluna estarão disponíveis para você no mapeamento de inspeção e de coletor.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Gerar um novo modelo a partir de colunas dinâmicas

A tabela dinâmica gera novos nomes de coluna dinamicamente com base em valores de linha. Você pode transformar essas novas colunas em metadados que podem ser referenciados posteriormente no fluxo de dados. Para fazer isso, clique na guia Visualização de dados. Todas as novas colunas geradas pela transformação dinâmica aparecem com um ícone "descompasso" no cabeçalho da tabela. Clique no botão "mapear descompassos" para transformar essas novas colunas em metadados, tornando-as parte do modelo do fluxo de dados.

![Colunas dinâmicas](media/data-flow/newpivot1.png "Mapear colunas dinâmicas descompassos")

### <a name="landing-new-columns-in-sink"></a>Novas colunas de aterrissagem no coletor

Mesmo com nomes de coluna dinâmicas em pivô, você ainda pode coletar seus novos nomes de coluna e valores em seu repositório de destino. Basta definir "permitir descompasso de esquema" em nas configurações do coletor. Você não verá os novos nomes dinâmicos em seus metadados de coluna, mas a opção de descompasso de esquema permitirá que você pouse os dados.

### <a name="view-metadata-in-design-mode"></a>Exibir metadados no modo de design

Se você quiser exibir os novos nomes de coluna como metadados na inspeção e desejar ver as colunas propagadas explicitamente para a transformação do coletor, defina valores explícitos na guia chave dinâmica.

### <a name="how-to-rejoin-original-fields"></a>Como reingressar nos campos originais
A transformação dinâmica só projetará as colunas usadas nas ações de agregação, agrupamento e dinamização. Se você quiser incluir as outras colunas da etapa anterior em seu fluxo, use uma nova ramificação da etapa anterior e use o padrão de autojunção para conectar o fluxo com os metadados originais.

## <a name="next-steps"></a>Próximos passos

Experimente a [transformação não dinâmica](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
