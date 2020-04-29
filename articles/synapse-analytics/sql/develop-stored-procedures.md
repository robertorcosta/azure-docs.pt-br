---
title: Usando procedimentos armazenados
description: Dicas para implementar procedimentos armazenados no pool Synapse SQL (data warehouse) para desenvolver soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a431df1ff4ef0984d1197933e7ca78979fa23089
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430975"
---
# <a name="using-stored-procedures-in-sql-pool"></a>Usando procedimentos armazenados no pool do SQL

Dicas para implementar procedimentos armazenados no pool Synapse SQL (data warehouse) para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar

O pool do SQL oferece suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho do pool do SQL, há também alguns recursos e funcionalidades que têm diferenças comportamentais e outras que não têm suporte.

## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular o código SQL, armazenando-o perto de seus dados no data warehouse. Os procedimentos armazenados ajudam os desenvolvedores a modularizarem suas soluções encapsulando o código em unidades gerenciáveis; facilitando a maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O pool do SQL fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Em data warehouses, o tempo de compilação é pequeno em comparação com o tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para grandes consultas. A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.

Quando o pool do SQL executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O pool SQL dá suporte a um máximo de oito níveis de aninhamento. Isso é um pouco diferente do SQL Server. O nível de aninhamento no SQL Server é de 32.

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

> [!NOTE]
> No momento, o pool do SQL não dá suporte a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). É necessário rastrear o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento, mas, se você fizer isso, será necessário trabalhar novamente em seu código para ajustar os níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

O pool do SQL não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa. Para obter um exemplo, consulte o artigo em [tabelas temporárias](develop-tables-temporary.md).

## <a name="limitations"></a>Limitações

Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no pool do SQL.

Eles são:

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

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
