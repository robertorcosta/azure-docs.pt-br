---
title: Usar procedimentos armazenados
description: Dicas para implementar procedimentos armazenados no SQL Synapse para desenvolver soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2089a6895a34c74de9d3e14beaa3807bbb1b6e6b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279070"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Usar procedimentos armazenados no SQL Synapse

Dicas para implementar procedimentos armazenados no pool do SQL Synapse para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar

Synapse SQL dá suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

Para manter a escala e o desempenho do pool do SQL, há também alguns recursos e funcionalidades que têm diferenças comportamentais e outras que não têm suporte.

## <a name="stored-procedures-in-synapse-sql"></a>Procedimentos armazenados no SQL Synapse

Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL e armazená-los perto dos seus dados no data warehouse. Os procedimentos armazenados ajudam os desenvolvedores a modularizar suas soluções encapsulando o código em unidades gerenciáveis, facilitando a maior Reusabilidade de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível. No exemplo a seguir, você pode ver os procedimentos que removem objetos externos, se existirem no banco de dados:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Esses procedimentos podem ser executados usando `EXEC` a instrução em que você pode especificar o nome do procedimento e os parâmetros:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

O Synapse SQL fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Em data warehouses, o tempo de compilação é pequeno em comparação com o tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para grandes consultas. A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.

Quando o Synapse SQL executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="encapsulate-validation-rules"></a>Encapsular regras de validação

Os procedimentos armazenados permitem que você localize a lógica de validação em um único módulo armazenado no banco de dados SQL. No exemplo a seguir, você pode ver como validar os valores dos parâmetros e alterar seus valores padrão.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

A lógica no procedimento SQL validará os parâmetros de entrada quando o procedimento for chamado.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.
Um exemplo de procedimento aninhado é mostrado no código a seguir:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Esse procedimento aceita um parâmetro que representa um nome e, em seguida, chama outros procedimentos para descartar os objetos com esse nome.
O pool SQL Synapse dá suporte a um máximo de oito níveis de aninhamento. Esse recurso é um pouco diferente de SQL Server. O nível de aninhamento no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento.

```sql
EXEC clean_up 'mytest'
```

Se o procedimento armazenado também criar outra chamada EXEC, o nível de aninhamento aumentará para dois.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Se o segundo procedimento executar algum SQL dinâmico, o nível de aninhamento aumentará para três.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> No momento, o SQL Synapse não dá suporte a [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). É necessário rastrear o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento, mas, se você fizer isso, será necessário trabalhar novamente em seu código para ajustar os níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

Synapse SQL não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. Há uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](develop-tables-temporary.md) para o pool do SQL Synapse provisionado.

## <a name="limitations"></a>Limitações

Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no SQL Synapse, como:

* procedimentos armazenados temporariamente
* procedimentos armazenados numerados
* procedimentos armazenados estendidos
* procedimentos armazenados de CLR
* opção de criptografia
* opção de replicação
* parâmetros com valor de tabela
* parâmetros somente leitura
* parâmetros padrão (no pool provisionado)
* contextos de execução
* Instrução return

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
