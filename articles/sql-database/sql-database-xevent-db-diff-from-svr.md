---
title: Eventos estendidos
description: Descreve eventos estendidos (XEvents) no Banco de Dados SQL do Azure e como as sessões de eventos diferem ligeiramente das sessões de eventos no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79213953"
---
# <a name="extended-events-in-sql-database"></a>Eventos estendidos no Banco de Dados SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica como a implementação de eventos estendidos no Banco de Dados SQL do Azure é um pouco diferente em comparação aos eventos estendidos no Microsoft SQL Server.

- O Banco de Dados SQL V12 recebeu o recurso de eventos estendidos na segunda metade de 2015.
- O SQL Server já tem eventos estendidos desde 2008.
- O conjunto de recursos de eventos estendidos no Banco de Dados SQL é um subconjunto robusto dos recursos do SQL Server.

*XEvents* é um apelido informal usado às vezes para "eventos estendidos" em blogs e outras fontes de informações.

Informações adicionais sobre eventos estendidos, para Banco de Dados SQL do Azure e Microsoft SQL Server, estão disponíveis em:

- [Início Rápido: eventos estendidos em SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Eventos estendidos](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que você já tem algum conhecimento de:

- [Serviço de banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).
- [Eventos estendidos](https://msdn.microsoft.com/library/bb630282.aspx) no Microsoft SQL Server.

- A maior parte da nossa documentação sobre eventos estendidos se aplica ao SQL Server e ao Banco de Dados SQL.

A exposição prévia aos itens a seguir é útil ao escolher o Arquivo de Evento como o [destino](#AzureXEventsTargets):

- [Serviço de Armazenamento do Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Usando o Azure PowerShell com o Armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) - fornece informações abrangentes sobre o PowerShell e o serviço de Armazenamento do Azure.

## <a name="code-samples"></a>Exemplos de código

Os tópicos relacionados fornecem dois exemplos de código:


- [Código de destino do Buffer de Anéis para eventos estendidos no Banco de Dados SQL](sql-database-xevent-code-ring-buffer.md)
    - Script curto e simples de Transact-SQL.
    - Enfatizamos no tópico do exemplo de código que, quando você concluir um destino de Buffer de Anéis, será necessário liberar seus recursos executando uma instrução alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Mais tarde, você poderá adicionar outra instância do Buffer de Anéis com `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Código de destino do Arquivo de evento para eventos estendidos no Banco de Dados SQL](sql-database-xevent-code-event-file.md)
    - A Fase 1 é PowerShell a fim de criar o contêiner de Armazenamento do Azure
    - Fase 2 é Transact-SQL que usa o contêiner de Armazenamento do Azure.

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL


- Ao executar o comando [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) no SQL Server, você usa a cláusula **ON SERVER** . Mas, no Banco de Dados SQL, você usa a cláusula **ON DATABASE** .


- A cláusula **ON DATABASE** também se aplica aos comandos Transact-SQL [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) e [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx).


- Uma prática recomendada é incluir a opção de sessão de evento de **STARTUP_STATE = ON** em sua instrução **CREATE EVENT SESSION** ou **ALTER EVENT SESSION**.
    - O valor **= ON** oferece suporte à reinicialização automática após a reconfiguração do banco de dados lógico devido a um failover.

## <a name="new-catalog-views"></a>Novas exibições do catálogo

O recurso de eventos estendidos recebe suporte de várias [exibições do catálogo](https://msdn.microsoft.com/library/ms174365.aspx). As exibições do catálogo mostram *metadados ou definições* de sessões de eventos criadas pelo usuário no banco de dados atual. As exibições não retornam informações sobre as instâncias de sessões de eventos ativas.

| Nome da<br/>exibição do catálogo | Descrição |
|:--- |:--- |
| **sys.database_event_session_actions** |Retorna uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** |Retorna uma linha para cada evento em uma sessão de eventos. |
| **sys.database_event_session_fields** |Retorna uma linha para cada coluna personalizável que foi explicitamente definida em eventos e destinos. |
| **sys.database_event_session_targets** |Retorna uma linha para cada destino de evento em uma sessão de evento. |
| **sys.database_event_sessions** |Retorna uma linha para cada sessão de eventos no banco de dados do Banco de Dados SQL. |

No Microsoft SQL Server, exibições do catálogo semelhantes têm nomes que incluem *.server\_* em vez de *.database\_*. O nome padrão é **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Novas exibições de gerenciamento dinâmico [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

O Banco de Dados SQL do Azure tem [exibições de gerenciamento dinâmico (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) que dão suporte a eventos estendidos. DMVs mostram as sessões de evento *ativas* .

| Nome da DMV | Descrição |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Retorna informações sobre ações da sessão de evento. |
| **sys.dm_xe_database_session_events** |Retorna informações sobre os eventos da sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração de objetos associados a uma sessão. |
| **sys. dm_xe_database_session_targets** |Retorna informações sobre os destinos da sessão. |
| **sys. dm_xe_database_sessions** |Retorna uma linha para cada sessão de evento com escopo no banco de dados atual. |

No Microsoft SQL Server, exibições de catálogo semelhantes são nomeadas sem a * \_* parte do banco de dados do nome, como:

- **sys.dm_xe_sessions**, em vez de nome<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs comuns a ambos
Para eventos estendidos, há DMVs adicionais que são comuns ao Banco de Dados SQL do Azure e ao Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Localizar os eventos estendidos, ações e destinos disponíveis

Você pode executar um simples SQL **SELECT** para obter uma lista dos evento, ações e destino disponíveis.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinos para as sessões de evento do Banco de Dados SQL

Estes são os destinos que podem capturar resultados de suas sessões de evento no Banco de Dados SQL:

- [Destino de Buffer de Anéis](https://msdn.microsoft.com/library/ff878182.aspx) - armazena dados na memória por um curto período.
- [Destino de Contador de eventos](https://msdn.microsoft.com/library/ff878025.aspx) - conta todos os eventos que ocorrem durante uma sessão de eventos estendidos.
- [Destino de Arquivo de evento](https://msdn.microsoft.com/library/ff878115.aspx) - grava buffers completos em um contêiner de Armazenamento do Azure.

A API [Rastreamento de Eventos para Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) não está disponível para eventos estendidos no Banco de Dados SQL.

## <a name="restrictions"></a>Restrições

Há algumas diferenças relacionadas à segurança condizentes com o ambiente de nuvem do Banco de Dados SQL:

- Os eventos estendidos são baseados no modelo de isolamento de locatário único. Uma sessão de eventos em um banco de dados não pode acessar dados ou eventos de outro banco de dados.
- Não é possível emitir uma instrução **CREATE EVENT SESSION** no contexto do banco de dados **mestre**.

## <a name="permission-model"></a>Modelo de permissão

Você deve ter permissão de **Controle** no banco de dados para emitir uma instrução **CREATE EVENT SESSION**. O proprietário do banco de dados (dbo) tem a permissão de **Controle** .

### <a name="storage-container-authorizations"></a>Autorizações de contêiner de armazenamento

O token SAS gerado para o contêiner de Armazenamento do Azure deve especificar **rwl** para as permissões. O valor **rwl** fornece as seguintes permissões:

- Ler
- Gravar
- Lista

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Há situações nas quais o uso intensivo de eventos estendidos pode acumular mais memória ativa do que o recomendado para a integridade geral do sistema. Portanto, o sistema do Banco de Dados SQL do Azure define e ajusta dinamicamente os limites para a quantidade de memória ativa que pode ser acumulada por uma sessão de eventos. Muitos fatores pesam no cálculo dinâmico.

Se você receber uma mensagem de erro informando que há uma imposição de quantidade máxima de memória, estas são algumas das ações corretivas possíveis:

- Executar menos sessões simultâneas do evento.
- Por meio das instruções **CREATE** e **ALTER** das sessões de evento, reduza a quantidade de memória que você especifica na cláusula **MAX\_MEMORY**.

### <a name="network-latency"></a>Latência da rede

O destino **Arquivo de Evento** pode enfrentar latência de rede ou falhas ao persistir dados para blobs de Armazenamento do Azure. Outros eventos no Banco de Dados SQL podem ser atrasados enquanto esperam a conclusão da comunicação de rede. Esse atraso pode retardar sua carga de trabalho.

- Para reduzir esse risco de desempenho, evite configurar a opção **EVENT_RETENTION_MODE** como **NO_EVENT_LOSS** nas definições de sua sessão de eventos.

## <a name="related-links"></a>Links relacionados

- [Usando Azure PowerShell com o armazenamento do Azure](../storage/common/storage-powershell-guide-full.md).
- [Cmdlets do Armazenamento do Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Usando o Azure PowerShell com o Armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) - fornece informações abrangentes sobre o PowerShell e o serviço de Armazenamento do Azure.
- [Como usar o Armazenamento de blob do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Postagens do blog de Jonathan Kehayias sobre eventos estendidos no Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- A página Web *Atualizações de Serviço* do Azure, limitada com o parâmetro para o Banco de Dados SQL do Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Outros tópicos com exemplos de código para eventos estendidos estão disponíveis nos links a seguir. No entanto, você deve verificar regularmente os exemplos para ver se eles se destinam ao Microsoft SQL Server ou ao Banco de Dados SQL do Azure. Em seguida, você pode decidir se alterações mínimas são necessárias para realizar o exemplo.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
