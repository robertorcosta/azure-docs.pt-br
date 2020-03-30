---
title: Junções nas consultas de log do Azure Monitor | Microsoft Docs
description: Este artigo inclui uma lição sobre como usar junções nas consultas de log do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670195"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Junções nas consultas de log do Azure Monitor

> [!NOTE]
> Você deve concluir Comece com as consultas [de log do Azure Monitor](get-started-portal.md) e do [Azure Monitor](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

As junções permitem analisar os dados de várias tabelas na mesma consulta. Elas mesclam as linhas dos dois conjuntos de dados fazendo a correspondência entre valores das colunas especificadas.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Neste exemplo, o primeiro conjunto de dados filtra todos os eventos de entrada. Isso é unido a um segundo conjunto de dados que filtra todos os eventos de saída. As colunas projetadas são _Computador_, _Conta_, _TargetLogonId_ e _TimeGenerated_. Os conjuntos de dados são correlacionados por uma coluna compartilhada, _TargetLogonId_. A saída é um único registro por correlação, que tem ambos o tempo de entrada e o tempo de saída.

Se ambos os conjuntos de dados tiverem colunas com os mesmos nomes, as colunas do conjunto de dados do lado direito receberia um número de índice, portanto, neste exemplo, os resultados mostrariam _TargetLogonId_ com os valores da tabela esquerda e _ TargetLogonId1_ com os valores da tabela direita. Nesse caso, a segunda coluna _TargetLogonId1_ foi removida usando o operador `project-away`.

> [!NOTE]
> Para melhorar o desempenho, mantenha apenas as colunas relevantes de conjuntos de dados unidos, usando o operador `project`.


Use a seguinte sintaxe para unir dois conjuntos de dados e a chave unida terá um nome diferente entre as duas tabelas:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Consultar Tabelas
Um uso comum de junções é usar um mapeamento estático dos valores usando `datatable`, o que pode ajudar a transformar os resultados na forma mais apresentável. Por exemplo, para aprimorar os dados do evento de segurança com o nome do evento para cada ID do evento.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Unir com uma datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Tipos de junção
Especifique o tipo de junção com o argumento _kind_. Cada tipo realiza uma correspondência diferente entre os registros das tabelas determinadas conforme descrito na tabela a seguir.

| Tipo de junção | Descrição |
|:---|:---|
| innerunique | Esse é o modo de junção padrão. Primeiro, os valores da coluna correspondente na tabela esquerda são encontrados e os valores duplicados são removidos.  Em seguida, o conjunto de valores exclusivos é correspondido com a tabela direita. |
| interna | Somente os registros correspondentes em ambas as tabelas são incluídos nos resultados. |
| leftouter | Todos os registros na tabela esquerda e os registros correspondentes na tabela direita são incluídos nos resultados. As propriedades de saída sem correspondência contêm valores nulos.  |
| leftanti | Os registros do lado esquerdo que não tenham correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas as colunas da tabela esquerda. |
| leftsemi | Os registros do lado esquerdo que tenham correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas as colunas da tabela esquerda. |


## <a name="best-practices"></a>Práticas recomendadas

Considere os pontos a seguir para o desempenho ideal:

- Use um filtro de tempo em cada tabela para reduzir os registros que devem ser avaliados para a junção.
- Use `where` e `project` para reduzir o número de linhas e colunas nas tabelas de entrada antes da junção.
- Se uma tabela sempre for menor do que a outra, use-a como o lado esquerdo da junção.


## <a name="next-steps"></a>Próximas etapas
Confira outras lições para utilizar consultas de log no Azure Monitor:

- [Operações da cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Gráficos](charts.md)
