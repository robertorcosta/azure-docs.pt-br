---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128750"
---
### <a name="event-system-properties-mapping"></a>Mapeamento de propriedades do sistema de eventos

> [!Note]
> * As propriedades do sistema são suportadas para eventos de registro único.
> * Para `csv` mapeamento, as propriedades são adicionadas no início do registro. Para `json` mapeamento, as propriedades são adicionadas de acordo com o nome que aparece na lista de paradas.

Se você selecionou propriedades do **sistema de eventos** na seção Origem de **dados** da tabela, você deve incluir as seguintes propriedades no esquema da tabela e no mapeamento.

**Exemplo de esquema de tabela**

Se seus dados incluem três`Timespan` `Metric`colunas `Value`( , e `x-opt-enqueued-time` ) `x-opt-offset`e as propriedades que você inclui são e, criar ou alterar o esquema da tabela usando este comando:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exemplo de mapeamento csv**

Execute os seguintes comandos para adicionar dados ao início do registro. Nota valores ordinários.

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
 
**Exemplo de mapeamento JSON**

Os dados são adicionados usando os nomes das propriedades do sistema como eles aparecem na lista de propriedades do **sistema de eventos da** lâmina de **conexão de dados.** Execute estes comandos:

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
