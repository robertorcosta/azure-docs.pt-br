---
title: Ajuste de desempenho com cache de conjunto de resultados
description: Visão geral do recurso de cache de conjunto de resultados para o SQL Analytics no Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350852"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ajuste de desempenho com cache de conjunto de resultados  
Quando o cache de conjunto de resultados é ativado, o SQL Analytics armazena automaticamente os resultados da consulta no banco de dados do usuário para uso repetitivo.  Isso permite que execuções de consulta subseqüentes obtenham resultados diretamente do cache persistido para que a recomputação não seja necessária.   O cache de conjunto de resultados melhora o desempenho da consulta e reduz o uso de recursos de computação.  Além disso, as consultas usando o conjunto de resultados em cache d não usam nenhum slots de simesmo e, portanto, não contam com os limites de concorrência existentes. Para segurança, os usuários só podem acessar os resultados armazenados em cache se tiverem as mesmas permissões de acesso a dados que os usuários que criam os resultados armazenados em cache.  

## <a name="key-commands"></a>Principais comandos
[Ativar/desativar o cache de conjunto de resultados para um banco de dados de usuário](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Ativar/desativar o cache definido para uma sessão](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Verifique o tamanho do conjunto de resultados armazenados em cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Limpe o cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>O que não é armazenado  

Quando o armazenamento em cache do conjunto de resultados estiver ATIVADO para um banco de dados, os resultados serão armazenados em cache para todas as consultas até o cache ficar cheio, exceto para estas consultas:
- Consultas que usam funções não determinísticas, como DateTime.Now()
- Consultas que usam funções definidas pelo usuário
- Consultas usando tabelas com segurança de nível de linha ou segurança de nível de coluna ativada
- Consultas que retornam dados com tamanho de linha maior que 64 KB

> [!IMPORTANT]
> As operações para criar cache conjunto de resultados e recuperar dados do cache acontecem no nó de controle de uma instância do SQL Analytics.
> Quando o cache do conjunto de resultados está ATIVADO, a execução de consultas que retornam um conjunto de resultados grande (por exemplo, mais de 1 milhão de linhas) pode causar alto uso da CPU no nó de controle e reduzir a resposta geral de consultas na instância.  Normalmente, essas consultas são usadas normalmente durante a exploração de dados ou operações de ETL. Para evitar sobrecarregar o nó de controle e causar problemas de desempenho, os usuários devem DESATIVAR o cache do conjunto de resultados no banco de dados antes de executar esses tipos de consultas.  

Execute esta consulta para o tempo decorrido pelas operações de cache set de resultados para uma consulta:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Aqui está uma saída de exemplo para uma consulta executada com o cache definido de resultado desativado.

![Query-passos-com-rsc-desativado](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Aqui está uma saída de exemplo para uma consulta executada com o cache definido de resultado ativado.

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando os resultados armazenados em cache são usados

O conjunto de resultados armazenado em cache será reutilizado em uma consulta se todos os requisitos a seguir forem atendidos:
- O usuário que executa a consulta tem acesso a todas as tabelas referenciadas na consulta.
- Há uma correspondência exata entre a nova consulta e a anterior que gerou o armazenamento em cache do conjunto de resultados.
- Não há alterações de dados ou esquemas nas tabelas em que o conjunto de resultados armazenado em cache foi gerado.

Execute esse comando para verificar se uma consulta foi executada com uma perda ou ocorrência no cache de resultado. Se houver uma ocorrência no cache, o result_cache_hit retornará 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gerenciar resultados em cache 

O tamanho máximo do cache de conjunto de resultados é de 1 TB por banco de dados.  Os resultados armazenados em cache são automaticamente invalidados quando os dados de consulta subjacentes mudam.  

O despejo de cache é gerenciado pelo SQL Analytics automaticamente seguindo esse cronograma: 
- A cada 48 horas, se o conjunto de resultados não foi usado ou foi invalidado. 
- Quando o cache definido de resultado se aproxima do tamanho máximo.

Os usuários podem esvaziar manualmente todo o cache do conjunto de resultados usando uma dessas opções: 
- DESATIVAR o recurso de cache do conjunto de resultados do banco de dados 
- Executar DBCC DROPRESULTSETCACHE enquanto estiver conectado ao banco de dados

Pausar um banco de dados não esvazia o conjunto de resultados armazenados em cache.  

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md). 
