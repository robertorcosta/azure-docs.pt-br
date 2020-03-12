---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128750"
---
### <a name="event-system-properties-mapping"></a>Mapeamento de propriedades do sistema de eventos

> [!Note]
> * As propriedades do sistema têm suporte para eventos de registro único.
> * Para o mapeamento de `csv`, as propriedades são adicionadas no início do registro. Para o mapeamento de `json`, as propriedades são adicionadas de acordo com o nome que aparece na lista suspensa.

Se você selecionou **Propriedades do sistema de eventos** na seção fonte de **dados** da tabela, deverá incluir as propriedades a seguir no esquema e no mapeamento da tabela.

**Exemplo de esquema de tabela**

Se os dados incluírem três colunas (`Timespan`, `Metric`e `Value`) e as propriedades incluídas forem `x-opt-enqueued-time` e `x-opt-offset`, crie ou altere o esquema da tabela usando este comando:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exemplo de mapeamento de CSV**

Execute os comandos a seguir para adicionar dados ao início do registro. Observe os valores ordinais.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Exemplo de mapeamento de JSON**

Os dados são adicionados usando os nomes de propriedades do sistema conforme aparecem na lista de **Propriedades do sistema de eventos** da folha conexão de **dados** . Execute estes comandos:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
