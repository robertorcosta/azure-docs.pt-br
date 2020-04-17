---
title: Deriva de esquema no mapeamento do fluxo de dados
description: Construa fluxos de dados resistentes no Azure Data Factory com descompasso de esquema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4c510cae7ba5a952e6dc7f7cb7ae7591fe801560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461286"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Deriva de esquema no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O esquema é o caso em que suas fontes frequentemente mudam metadados. Campos, colunas e tipos podem ser adicionados, removidos ou alterados na hora. Sem o manuseio para a deriva de esquemas, seu fluxo de dados torna-se vulnerável a alterações na fonte de dados upstream. Padrões típicos de ETL falham quando as colunas e campos de entrada mudam porque tendem a ser vinculados a esses nomes de origem.

Para proteger contra a deriva de esquemas, é importante ter as instalações em uma ferramenta de fluxo de dados para permitir que você, como engenheiro de dados, para:

* Defina fontes que tenham nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Defina parâmetros de transformação que funcionam com padrões de dados ao invés de campos e valores codificados
* Defina expressões que compreendam padrões para corresponder aos campos recebidos, ao invés de usar campos nomeados

A Fábrica de Dados do Azure suporta nativamente esquemas flexíveis que mudam de execução para execução para que você possa construir uma lógica genérica de transformação de dados sem a necessidade de recompilar seus fluxos de dados.

Você precisa tomar uma decisão sobre a arquitetura de seu fluxo de dados para aceitar o descompasso de esquema em todo o fluxo. Ao fazer isso, você pode proteger contra alterações de esquema das fontes. No entanto, você perderá a vinculação antecipada de suas colunas e tipos ao longo de seu fluxo de dados. A Azure Data Factory trata os fluxos de deriva do esquema como fluxos de ligação tardia, de modo que quando você constrói suas transformações, os nomes das colunas à deriva não estarão disponíveis para você nas visualizações do esquema em todo o fluxo.

Este vídeo fornece uma introdução a algumas das soluções complexas que você pode construir facilmente no ADF com o recurso de deriva de esquema do fluxo de dados. Neste exemplo, construímos padrões reutilizáveis com base em esquemas flexíveis de banco de dados:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Deriva de esquema na fonte

As colunas que entram no fluxo de dados da sua definição de origem são definidas como "drifted" quando não estão presentes na projeção de origem. Você pode visualizar sua projeção de origem a partir do guia de projeção na transformação da fonte. Quando você selecionar um conjunto de dados para sua fonte, o ADF pegará automaticamente o esquema do conjunto de dados e criará um projeto a partir dessa definição de esquema do conjunto de dados.

Em uma transformação de origem, o deriva do esquema é definido como colunas de leitura que não são definidas como esquema do conjunto de dados. Para habilitar a deriva do esquema, verifique **Permitir a deriva do esquema** na sua transformação de origem.

![Fonte de deriva de esquema](media/data-flow/schemadrift001.png "Fonte de deriva de esquema")

Quando a deriva do esquema é ativada, todos os campos de entrada são lidos da sua fonte durante a execução e passam por todo o fluxo para a Pia. Por padrão, todas as colunas recém-detectadas, conhecidas como *colunas derivadas,* chegam como um tipo de dados de seqüência. Se desejar que seu fluxo de dados infera automaticamente os tipos de dados das colunas derivadas, verifique os tipos de **colunas derivadas infer** em suas configurações de origem.

## <a name="schema-drift-in-sink"></a>Deriva de esquema na pia

Em uma transformação de pia, a deriva do esquema é quando você escreve colunas adicionais em cima do que é definido no esquema de dados da pia. Para habilitar a deriva do esquema, verifique **Permitir a deriva do esquema** na transformação da pia.

![Pia de deriva de esquema](media/data-flow/schemadrift002.png "Pia de deriva de esquema")

Se a deriva do esquema estiver ativada, **certifique-se de** que o controle deslizante de mapeamento automático na guia Mapeamento esteja ativado. Com este controle deslizante ligado, todas as colunas recebidas são escritas para o seu destino. Caso contrário, você deve usar mapeamento baseado em regras para escrever colunas à deriva.

![Mapeamento automático do sink](media/data-flow/automap.png "Mapeamento automático do sink")

## <a name="transforming-drifted-columns"></a>Transformando colunas à deriva

Quando o fluxo de dados tiver derivado colunas, você pode acessá-las em suas transformações com os seguintes métodos:

* Use `byPosition` as `byName` expressões e expressões para referenciar explicitamente uma coluna por nome ou número de posição.
* Adicione um padrão de coluna em uma transformação Derivada ou Agregada para corresponder a qualquer combinação de nome, fluxo, posição ou tipo
* Adicione mapeamento baseado em regras em uma transformação Select ou Sink para combinar colunas derivadas a alias de colunas através de um padrão

Para obter mais informações sobre como implementar padrões de coluna, consulte [Padrões de coluna no mapeamento do fluxo de dados](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Mapa de colunas de deriva ação rápida

Para referenciar explicitamente colunas à deriva, você pode gerar rapidamente mapeamentos para essas colunas através de uma ação rápida de visualização de dados. Uma vez que o [modo de depuração](concepts-data-flow-debug-mode.md) esteja ligado, vá para a guia Visualização de dados e clique **em Atualizar** para obter uma visualização de dados. Se a fábrica de dados detectar que existem colunas derivadas, você pode clicar em **Map Drifted** e gerar uma coluna derivada que permite que você faça referência a todas as colunas derivadas em exibições de esquema a jusante.

![Mapa à deriva](media/data-flow/mapdrifted1.png "Mapa à deriva")

Na transformação gerada da Coluna Derivada, cada coluna derivada é mapeada para seu nome e tipo de dados detectados. Na visualização de dados acima, a coluna 'movieId' é detectada como um inteiro. Depois **que Map Drifted** é clicado, movieId `toInteger(byName('movieId'))` é definido na Coluna Derivada como e incluído em exibições de esquema em transformações a jusante.

![Mapa à deriva](media/data-flow/mapdrifted2.png "Mapa à deriva")

## <a name="next-steps"></a>Próximas etapas
Na [Linguagem de Expressão de Fluxo de Dados,](data-flow-expression-functions.md)você encontrará instalações adicionais para padrões de coluna e deriva de esquema, incluindo "byName" e "byPosition".
