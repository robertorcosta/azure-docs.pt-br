---
title: Usando procedimentos armazenados
description: Dicas para desenvolver soluções implementando procedimentos armazenados no pool SQL do Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3ffdf7a66c2562b43fc2ed02bb088ab1095118fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416170"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Usando procedimentos armazenados no pool do SQL Synapse

Este artigo fornece dicas para o desenvolvimento de soluções de pool do SQL implementando procedimentos armazenados.

## <a name="what-to-expect"></a>O que esperar

O pool do SQL oferece suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

Além disso, para ajudá-lo a manter a escala e o desempenho do pool do SQL, há recursos e funcionalidades adicionais que têm diferenças comportamentais.

## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL, que é armazenado perto de seus dados de pool do SQL. Os procedimentos armazenados também ajudam os desenvolvedores a modularizar suas soluções encapsulando o código em unidades gerenciáveis, facilitando assim a reutilização de código maior. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O pool do SQL fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em comparação com SQL Server é que o procedimento armazenado não é um código pré-compilado.

Em geral, para data warehouses, o tempo de compilação é pequeno em comparação ao tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para consultas grandes.

> [!TIP]
> A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é útil considerar os procedimentos armazenados como contêineres para a lógica do SQL.

Quando o pool do SQL executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O pool SQL dá suporte a um máximo de oito níveis de aninhamento. Por outro lado, o nível de aninhamento no SQL Server é 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento.

```sql
EXEC prc_nesting
```

Se o procedimento armazenado também criar outra chamada EXEC, o nível de aninhamento aumentará para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Se o segundo procedimento executar algum SQL dinâmico, o nível de aninhamento aumentará para três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

No momento, o pool SQL não dá suporte a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Assim, você precisa acompanhar o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento. Mas, se você fizer isso, precisará retrabalhar seu código para se ajustar aos níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

O pool SQL não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. Há, no entanto, uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Limitações

Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no pool do SQL, que são os seguintes:

* procedimentos armazenados temporariamente
* procedimentos armazenados numerados
* procedimentos armazenados estendidos
* procedimentos armazenados de CLR
* opção de criptografia
* opção de replicação
* parâmetros com valor de tabela
* parâmetros somente leitura
* parâmetros padrão
* contextos de execução
* instrução return

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
