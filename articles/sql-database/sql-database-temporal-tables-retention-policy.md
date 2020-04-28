---
title: Gerenciar dados históricos em tabelas temporais
description: Aprenda a usar a política de retenção temporal para manter dados históricos sob seu controle.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820678"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerenciar dados históricos em Tabelas Temporais com a política de retenção

Tabelas Temporais podem aumentar o tamanho do banco de dados mais do que tabelas regulares, especialmente se você mantiver os dados históricos por um período maior de tempo. Portanto, a política de retenção de dados históricos é um aspecto importante do planejamento e gerenciamento do ciclo de vida de cada tabela temporal. Tabelas temporais no Banco de Dados SQL do Azure vem com um mecanismo de retenção de fácil utilização que ajuda você a realizar essa tarefa.

A retenção de histórico temporal pode ser configurado no nível de tabela individual, que permite aos usuários criar políticas de vencimento flexíveis. A aplicação de retenção temporal é simples: requer apenas um parâmetro a ser definido durante a criação da tabela ou alteração do esquema.

Depois de definir a política de retenção, o Banco de Dados SQL do Azure começa a verificar regularmente se há linhas de histórico qualificadas para a limpeza automática de dados. A identificação das linhas correspondentes e sua remoção da tabela de histórico ocorrem de forma transparente na tarefa em segundo plano que é agendada e executada pelo sistema. A condição de vencimento para as linhas da tabela de histórico é verificada com base na coluna que representa o final do período SYSTEM_TIME. Se o período de retenção for definido como seis meses, por exemplo, as linhas de tabela qualificadas para limpeza atenderão a seguinte condição:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

No exemplo anterior, supomos que a coluna **ValidTo** corresponde ao final do período de SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção

Antes de configurar a política de retenção para uma tabela temporal, verifique primeiro se a retenção de histórico temporal está habilitada *no nível do banco de dados*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

O sinalizador de banco de dados **is_temporal_history_retention_enabled** é definido como ON por padrão, mas os usuários podem alterá-lo com a instrução ALTER DATABASE. Ele é automaticamente definido como OFF após a operação [restauração pontual](sql-database-recovery-using-backups.md). Para habilitar a limpeza da retenção de histórico temporal para seu banco de dados, execute a seguinte instrução:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Será possível configurar a retenção para tabelas temporais mesmo se **is_temporal_history_retention_enabled** estiver DESATIVADO, mas a limpeza automática de linhas antigas não será disparada nesse caso.

A política de retenção é configurada durante a criação de uma tabela especificando o valor do parâmetro HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

O Banco de Dados SQL do Azure permite especificar o período de retenção usando unidades temporais diferentes: DIAS, SEMANAS, MESES e ANOS. Se HISTORY_RETENTION_PERIOD for omitido, a retenção será considerada INFINITE (infinita). Também é possível usar a palavras-chave INFINITE explicitamente.

Em alguns cenários, pode ser útil configurar a retenção após a criação da tabela ou alterar o valor configurado anteriormente. Neste caso, use a instrução ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Definir SYSTEM_VERSIONING como OFF *não preserva* o valor do período de retenção. Definir SYSTEM_VERSIONING como ON sem HISTORY_RETENTION_PERIOD explicitamente especificado resulta no período de retenção INFINITO.

Para examinar o estado atual da política de retenção, use a seguinte consulta que une o sinalizador de habilitação de retenção temporal no nível do banco de dados com períodos de retenção para tabelas individuais:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>Como o Banco de Dados SQL exclui linhas antigas

O processo de limpeza depende do layout do índice da tabela de histórico. É importante observar que *somente tabelas de histórico com um índice clusterizado (árvore B ou columnstore) podem ter uma política de retenção finita configurada*. Uma tarefa em segundo plano é criada para executar a limpeza de dados antigos para todas as tabelas temporais com período de retenção finito.
Lógica de limpeza para o índice clusterizado de rowstore (árvore B) exclui a linha antiga em partes menores (até 10 K), minimizando a pressão no log do banco de dados e o subsistema de E/S. Embora a lógica de limpeza utilize o índice de árvore B necessário, a ordem das exclusões para as linhas mais antigas que o período de retenção não pode ser garantido com certeza. Portanto, *não assuma nenhuma dependência na ordem de limpeza em seus aplicativos*.

A tarefa de limpeza para o columnstore clusterizado remove [grupos de linhas](https://msdn.microsoft.com/library/gg492088.aspx) inteiros de uma vez (normalmente contém 1 milhão de linhas cada), o que é muito eficiente, especialmente quando dados históricos são gerados em um ritmo alto.

![Retenção de columnstore clusterizado](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

A excelente compactação de dados e eficiente limpeza da retenção torna o índice de columnstore clusterizado uma opção ideal para cenários em que sua carga de trabalho gera rapidamente uma grande quantidade de dados históricos. Esse padrão é típico para o [cargas de trabalho de processamento transacional intensas que usam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para controle de alterações e auditoria, análise de tendências ou ingestão de dados de IoT.

## <a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza para tabelas com um índice clusterizado de rowstore requer que um índice inicie a coluna correspondente ao fim do período de SYSTEM_TIME. Se esse índice não existir, você não poderá configurar o período de retenção finito:

*MSG 13765, nível 16, estado 1 <br> </br> falha na configuração do período de retenção finito na tabela temporal com controle de versão do sistema ' temporalstagetestdb. dbo. WebsiteUserInfo ' porque a tabela de histórico ' temporalstagetestdb. dbo. WebsiteUserInfoHistory ' não contém o índice clusterizado necessário. Considere criar um índice columnstore ou árvore B clusterizado começando com a coluna que corresponde ao fim do período de SYSTEM_TIME, na tabela de histórico.*

É importante observar que a tabela de histórico padrão criada pelo Banco de Dados SQL do Azure já possui um índice clusterizado compatível com a política de retenção. Se você tentar remover o índice em uma tabela com o período de retenção finito, a operação falhará com o seguinte erro:

*MSG 13766, nível 16, estado 1 <br> </br> não é possível descartar o índice clusterizado ' WebsiteUserInfoHistory. IX_WebsiteUserInfoHistory ' porque ele está sendo usado para limpeza automática de dados antigos. Considere definir HISTORY_RETENTION_PERIOD como infinito na tabela temporal com versão do sistema correspondente se você precisar descartar esse índice.*

A limpeza no índice columnstore clusterizado funciona de maneira ideal se as linhas históricas forem inseridas em ordem crescente (ordenadas no final da coluna do período), que sempre será o caso quando a tabela de histórico for populada exclusivamente pelo mecanismo SYSTEM_VERSIONIOING. Se as linhas da tabela de histórico não forem ordenadas pelo final da coluna do período (o que pode ser o caso se você migrar dados históricos existentes), você deverá recriar o índice columnstore clusterizado além do índice rowstore de árvore B ordenado corretamente para obter o desempenho ideal.

Evite recriar índices columnstore clusterizados na tabela de histórico com o período de retenção finito, pois isso pode mudar a ordem dos grupos de linhas naturalmente imposta pela operação de controle de versão do sistema. Se você precisar recriar o índice columnstore clusterizado na tabela de histórico, faça-o recriando-o junto com o índice de árvore B compatível, preservando a ordem dos rowgroups necessários para a limpeza de dados regulares. A mesma abordagem deve ser usada se você criar uma tabela temporal com a tabela de histórico existente que tem um índice de coluna clusterizado sem ordem de dados garantida:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando o período de retenção finito estiver configurado para a tabela de histórico com o índice columnstore clusterizado, você não poderá criar índices adicionais de árvore B não clusterizado na tabela:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Uma tentativa de executar a instrução acima falhará com o seguinte erro:

*MSG 13772, nível 16, estado 1 <br> </br> não é possível criar um índice não clusterizado em uma tabela de histórico temporal ' WebsiteUserInfoHistory ', pois ela tem um período de retenção finito e um índice columnstore clusterizado definido.*

## <a name="querying-tables-with-retention-policy"></a>Consultando tabelas com a política de retenção

Todas as consultas na tabela temporal filtram automaticamente as linhas históricas que correspondem à política de retenção finita para evitar resultados imprevisíveis e inconsistentes, pois as linhas antigas podem ser excluídas pela tarefa de limpeza *a qualquer momento e em qualquer ordem*.

A figura a seguir mostra o plano de consulta para uma consulta simples:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

O plano de consulta inclui filtro adicional aplicado ao final da coluna do período (ValidTo) no operador Clustered Index Scan na tabela de histórico (realçada). Este exemplo supõe que o período de retenção de um MÊS foi definido na tabela WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se você consultar diretamente a tabela de histórico, verá linhas que são mais antigas do que o período de retenção especificado, mas sem qualquer garantia de resultados da consulta repetíveis. A figura a seguir mostra o plano de execução de consulta para a consulta na tabela de histórico sem filtros adicionais aplicados:

![Consultando histórico sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

A lógica de negócios não deve contar com a leitura da tabela de histórico além do período de retenção, pois resultados inconsistentes ou inesperados podem ser obtidos. É recomendável usar consultas temporais com a cláusula FOR SYSTEM_TIME para análise de dados em tabelas temporais.

## <a name="point-in-time-restore-considerations"></a>Considerações da recuperação pontual

Quando você cria o novo banco de dados [restaurando o banco de dados existente para um momento específico ](sql-database-recovery-using-backups.md), ele tem uma retenção temporal desabilitada no nível do banco de dados. (O sinalizador **is_temporal_history_retention_enabled** está definido como DESATIVADO). Essa funcionalidade permite examinar todas as linhas de histórico na restauração, sem se preocupar se linhas antigas são removidas antes de você chegar a consultá-las. Você pode usá-la para *inspecionar dados históricos além do período de retenção configurado*.

Digamos que uma tabela temporal tem o período de retenção de um MÊS especificado. Se seu banco de dados foi criado na camada de serviço Premium, você poderá criar a cópia de banco de dados com o estado do banco de dados de até 35 dias anteriores. Isso efetivamente permitiria analisar linhas históricas de até 65 dias anteriores consultando a tabela de histórico diretamente.

Se você desejar ativar a limpeza da retenção temporal, execute a seguinte instrução Transact-SQL após a restauração pontual:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Próximas etapas

Para saber como usar tabelas temporais em seus aplicativos, confira [introdução com tabelas temporais no banco de dados SQL do Azure](sql-database-temporal-tables.md).

Visite o Channel 9 para ouvir uma [história de sucesso real de implementação temporal do cliente](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assista a uma [demonstração temporal dinâmica](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre as Tabelas Temporais, leia a [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
