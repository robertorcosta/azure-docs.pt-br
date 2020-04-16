---
title: Classificar a transformação no mapeamento do fluxo de dados
description: Transformação do sort de classificação de dados da fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 381c6573dff1b3f1638af9090a535d9a1e59b2b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413154"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Classificar a transformação no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A transformação de classificação permite classificar as linhas recebidas no fluxo de dados atual. Você pode escolher colunas individuais e classificá-las em ordem ascendente ou descendente.

> [!NOTE]
> Os fluxos de dados de mapeamento são executados em clusters de faíscas que distribuem dados em vários nós e partições. Se você optar por reparticionar seus dados em uma transformação subseqüente, você pode perder sua classificação devido à reformulação de dados.

## <a name="configuration"></a>Configuração

![Classificar configurações](media/data-flow/sort.png "Classificar")

**Caso insensível:** Quer você queira ou não ignorar o caso ao classificar os campos de string ou texto

**Classificar somente dentro de partições:** À medida que os fluxos de dados são executados na faísca, cada fluxo de dados é dividido em partições. Essa configuração classifica os dados apenas dentro das partições recebidas em vez de classificar todo o fluxo de dados. 

**Classificar condições:** Escolha quais colunas você está classificando e em que ordem o tipo acontece. A ordem determina a prioridade de classificação. Escolha se as nulas aparecerão ou não no início ou no final do fluxo de dados.

### <a name="computed-columns"></a>Colunas computadas

Para modificar ou extrair um valor de coluna antes de aplicar o tipo, fique por cima da coluna e selecione "coluna computada". Isso abrirá o construtor de expressão para criar uma expressão para a operação de classificação em vez de usar um valor de coluna.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Classificar configurações](media/data-flow/sort.png "Classificar")

O script de fluxo de dados para a configuração de classificação acima está no trecho de código abaixo.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Próximas etapas

Depois da classificação, você pode querer usar a [Transformação Agregada](data-flow-aggregate.md)
