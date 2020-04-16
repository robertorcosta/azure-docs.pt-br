---
title: Usando procedimentos armazenados
description: Dicas para desenvolver soluções implementando procedimentos armazenados no pool Synapse SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416170"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Usando procedimentos armazenados no pool Synapse SQL

Este artigo fornece dicas para o desenvolvimento de soluções de pool SQL, implementando procedimentos armazenados.

## <a name="what-to-expect"></a>O que esperar

O pool SQL suporta muitos dos recursos T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

Além disso, para ajudá-lo a manter a escala e o desempenho do pool SQL, existem recursos adicionais e funcionalidades que têm diferenças comportamentais.

## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL, que é armazenado perto dos dados do pool SQL. Os procedimentos armazenados também ajudam os desenvolvedores a modular suas soluções encapsulando o código em unidades gerenciáveis, facilitando assim maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O pool SQL fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em relação ao SQL Server é que o procedimento armazenado não é um código pré-compilado.

Em geral, para data warehouses, o tempo de compilação é pequeno em comparação com o tempo que leva para executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado seja corretamente otimizado para grandes consultas.

> [!TIP]
> A meta é poupar horas, minutos e segundos, não milissegundos. Então, é útil pensar nos procedimentos armazenados como recipientes para a lógica SQL.

Quando o pool SQL executa seu procedimento armazenado, as instruções SQL são analisados, traduzidos e otimizados em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O pool SQL suporta um máximo de oito níveis de aninhamento. Em contraste, o nível de ninho no SQL Server é de 32.

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

O pool SQL não suporta atualmente [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Como tal, você precisa rastrear o nível do ninho. É improvável que você exceda o limite de nível de oito ninhos. Mas, se você fizer isso, você precisa retrabalhar seu código para se encaixar nos níveis de aninhamento dentro deste limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

O pool SQL não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. Há, no entanto, uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Limitações

Existem alguns aspectos dos procedimentos armazenados transact-SQL que não são implementados no pool SQL, que são os seguintes:

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
