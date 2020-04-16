---
title: Achatar a transformação no mapeamento do fluxo de dados
description: Desnormalizar dados hierárquicos usando a transformação de achatamento
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413685"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Achatar a transformação no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação de achate para levar valores de matriz dentro de estruturas hierárquicas como JSON e desenrolá-los em linhas individuais. Esse processo é conhecido como desnormalização.

## <a name="configuration"></a>Configuração

A transformação do achatamento contém as seguintes configurações

![Configurações de achate](media/data-flow/flatten1.png "Configurações de achate")

### <a name="unroll-by"></a>Unroll por

Selecione uma matriz para desenrolar. Os dados de saída terão uma linha por item em cada matriz. Se o desenrolar por matriz na linha de entrada estiver nulo ou vazio, haverá uma linha de saída com valores desenrolados como nulos.

### <a name="unroll-root"></a>Raiz de desenrolar

Por padrão, a transformação de achate desenrola uma matriz para o topo da hierarquia em que existe. Você pode selecionar opcionalmente uma matriz como sua raiz de desenrolar. A raiz de desenrolar deve ser uma matriz de objetos complexos que são ou contêm o desenrolar por matriz. Se uma raiz de desenrolar for selecionada, os dados de saída conterão pelo menos uma linha por item na raiz de desenrolar. Se a linha de entrada não tiver nenhum item na raiz de desrolo, ela será descartada dos dados de saída. Escolher uma raiz de sumido sempre produzirá um número menor ou igual de linhas do que o comportamento padrão.

### <a name="flatten-mapping"></a>Mapeamento de achatamento

Semelhante à transformação seleto, escolha a projeção da nova estrutura a partir de campos de entrada e da matriz desnormalizada. Se uma matriz desnormalizada for mapeada, a coluna de saída será o mesmo tipo de dados da matriz. Se o desrolo por matriz for uma matriz de objetos complexos que contenham submatrizes, mapear um item desse subarry produzirá uma matriz.

Consulte a guia de inspeção e a visualização de dados para verificar a saída de mapeamento.

## <a name="examples"></a>Exemplos

Consulte o seguinte objeto JSON para obter os exemplos abaixo da transformação do achatamento

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Sem raiz de desenrolar com matriz de cordas

| Unroll por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| bens.clientes | Nenhum | name <br> cliente = goods.customer |

#### <a name="output"></a>Saída

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Sem raiz de desenrolar com matriz complexa

| Unroll por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItens | Nenhum | name <br> orderId = goods.orders.orderId <br> itemNome = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> localização = localização |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Mesma raiz que unroll array

| Unroll por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| bens.ordens | bens.ordens | name <br> goods.orders.shipped.orderItems.itemName <br> bens.clientes <br> local |

#### <a name="output"></a>Saída

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Desfazer raiz com matriz complexa

| Unroll por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | bens.ordens |name <br> orderId = goods.orders.orderId <br> itemNome = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> localização = localização |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

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

### <a name="example"></a>Exemplo

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Próximas etapas

* Use a [transformação pivô](data-flow-pivot.md) para linhas pivôs em colunas.
* Use a [transformação Unpivot](data-flow-unpivot.md) para colunas pivôs em linhas.
