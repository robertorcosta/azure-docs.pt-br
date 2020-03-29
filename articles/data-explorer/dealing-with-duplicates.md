---
title: Manuseie dados duplicados no Azure Data Explorer
description: Este tópico mostrará várias abordagens para lidar com dados duplicados ao usar o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608234"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Manuseie dados duplicados no Azure Data Explorer

Os dispositivo que enviam dados para a nuvem mantêm um cache local dos dados. Dependendo do volume dos dados, o cache local pode armazenar dados por dias, e até meses. Você deseja proteger os seus bancos de dados analíticos contra o funcionamento incorreto de dispositivos que reenviam os dados armazenados em cache e causam a duplicação dos dados no banco de dados analítico. Este tópico descreve as melhores práticas para tratar de dados duplicados nesses tipos de cenário.

A melhor solução para a duplicação de dados é impedi-la. Se possível, corrija o problema antecipadamente no pipeline de dados, o que economiza os custos associados à movimentação de dados ao longo do pipeline de dados e evita o gasto de recursos copiando dados duplicados ingeridos pelo sistema. No entanto, em situações em que o sistema de origem não pode ser modificado, há várias maneiras de lidar com esse cenário.

## <a name="understand-the-impact-of-duplicate-data"></a>Entender o impacto dos dados duplicados

Monitore a porcentagem dos dados duplicados. Após descobrir a porcentagem de dados duplicados, você pode analisar o escopo do problema e o impacto nos negócios e escolher a solução adequada.

Amostra de consulta para identificar a porcentagem de registros duplicados:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>soluções para lidar com dados duplicados

### <a name="solution-1-dont-remove-duplicate-data"></a>solução #1: Não remova dados duplicados

Entenda os requisitos de negócios e a tolerância dos dados duplicados. Alguns conjunto de dados podem ser gerenciados com uma determinada porcentagem de dados duplicados. Se os dados duplicados não têm impacto significativo, você pode ignorá-los. A vantagem de não remover os dados duplicados é não ter sobrecarga adicional no processo de ingestão nem no desempenho da consulta.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>solução #2: Manuseie linhas duplicadas durante a consulta

Outra opção é filtrar as linhas duplicadas nos dados durante a consulta. A [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) função agregada pode ser usada para filtrar os registros duplicados e retornar o último registro com base no carimbo de data e hora (ou outra coluna). A vantagem de usar esse método é a ingestão mais rápida, uma vez que a eliminação de duplicação ocorre durante o tempo de consulta. Além disso, todos os registros (incluindo duplicatas) estão disponíveis para auditoria e solução de problemas. A desvantagem de usar a função `arg_max` é o tempo de consulta adicional e a carga na CPU todas as vezes que os dados são consultados. Dependendo da quantidade de dados que está sendo consultada, essa solução pode se tornar não funcional ou consumir muita memória, o que exige a alternação para outras opções.

No seguinte exemplo, consultamos o último registro ingerido para um conjunto de colunas que determina os registros exclusivos:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Essa consulta também pode ser colocada dentro de uma função em vez de ser feita diretamente na tabela:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Solução #3: Filtrar duplicatas durante o processo de ingestão

Outra solução é filtrar duplicatas durante o processo de ingestão. O sistema ignora os dados duplicados durante a ingestão nas tabelas Kusto. Os dados são ingeridos em uma tabela de preparo e copiados em outra tabela após a remoção das linhas duplicadas. A vantagem dessa solução é que o desempenho da consulta melhora consideravelmente em relação à solução anterior. As desvantagens incluem aumento do tempo de ingestão e custos adicionais com o armazenamento de dados. Além disso, essa solução só funciona se as duplicações não forem ingeridas simultaneamente. Se houver várias ingessitions simultâneas contendo registros duplicados, todos podem ser ingeridos, uma vez que o processo de deduplicação não encontrará nenhum registro correspondente existente na tabela.    

O seguinte exemplo descreve esse método:

1. Crie outra tabela com o mesmo esquema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. crie uma função para filtrar os registros duplicados não ingressando os novos registros com os ingeridos anteriormente.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Os ingressos são operações limitadas à CPU e adicionam uma carga extra ao sistema.

1. Defina a [Política de Atualização](/azure/kusto/management/update-policy) na tabela `DeviceEventsUnique`. A política de atualização é ativada quando novos dados entram na tabela `DeviceEventsAll`. O mecanismo Kusto executará automaticamente a função à medidas que novas [extensões](/azure/kusto/management/extents-overview) forem criadas. O processamento é colocado no escopo de dados recém-criados. O comando a seguir une a tabela de origem (`DeviceEventsAll`), a tabela de destino (`DeviceEventsUnique`) e a função `RemoveDuplicatesDeviceEvents` para criar a política de atualização.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > A política de atualização estende a duração da ingestão, pois os dados são filtrados durante a ingestão e, em seguida, ingeridos duas vezes (na tabela `DeviceEventsAll` e na tabela `DeviceEventsUnique`).

1. (Opcional) Defina uma retenção de dados menor na tabela `DeviceEventsAll` para evitar o armazenamento de cópias dos dados. Escolha o número de dias de acordo com o volume de dados e por quanto tempo você deseja reter dados para a solução de problemas. É possível defini-lo para retenção de `0d` dias, de modo a poupar COGS e melhorar o desempenho, uma vez que os dados não são carregados no armazenamento.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Resumo

A duplicação de dados pode ser tratada de várias maneiras. Avalie as opções atentamente, levando em conta o preço e o desempenho para determinar o método correto para os seus negócios.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escrever consultas para O Azure Data Explorer](write-queries.md)
