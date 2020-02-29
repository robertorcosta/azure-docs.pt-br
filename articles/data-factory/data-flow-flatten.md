---
title: Mapeando a transformação achatar fluxo de dados
description: Transformação do achatamento do fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164722"
---
# <a name="azure-data-factory-flatten-transformation"></a>Transformação de Azure Data Factory achatamento

A transformação achatamento pode ser usada para dinamizar valores de matriz dentro de uma estrutura hierárquica em novas linhas, essencialmente desnormalizando seus dados.

![Caixa de ferramentas transformação](media/data-flow/flatten5.png "Caixa de ferramentas transformação")

![Transformação achatada 1](media/data-flow/flatten7.png "Transformação achatada 1")

## <a name="unroll-by"></a>Não acumulado por

Primeiro, escolha a coluna de matriz que você deseja reverter e dinamizar.

![Configurações de transformação de mesclagem](media/data-flow/flatten1.png "Configurações de transformação de mesclagem")

## <a name="unroll-root"></a>Desacumular raiz

Por padrão, o ADF mesclará a estrutura na matriz de redistribuição que você escolheu acima. Ou, você pode escolher uma parte diferente da hierarquia para a qual reverter. "Unlance root" é uma configuração opcional.

## <a name="input-columns"></a>Colunas de entrada

Por fim, escolha a projeção da sua nova estrutura com base nos campos de entrada, bem como a coluna normalizada que você não rolou.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>{1&gt;Exemplo&lt;1}

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Use a [transformação dinâmica](data-flow-pivot.md) para dinamizar linhas para colunas.
* Use a [transformação não dinâmica](data-flow-unpivot.md) para dinamizar as colunas para linhas.
