---
title: Atribuir variáveis com Synapse SQL
description: Neste artigo, você encontrará dicas para atribuir variáveis T-SQL com Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428661"
---
# <a name="assigning-variables-with-synapse-sql"></a>Atribuindo variáveis com Synapse SQL

Neste artigo, você encontrará dicas para atribuir variáveis T-SQL com Synapse SQL.

## <a name="setting-variables-with-declare"></a>Definição de variáveis com DECLARE

Variáveis em Synapse SQL são definidas usando a `DECLARE` instrução ou a `SET` instrução. Inicializar variáveis com DECLARE é uma das maneiras mais flexíveis de definir um valor de variável em Synapse SQL.

```sql
DECLARE @v  int = 0
;
```

Você também pode usar DECLARE para definir mais de uma variável por vez. Você não pode usar SELECT ou UPDATE para fazer o seguinte:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Você não pode inicializar e usar uma variável na mesma instrução DECLARE. Para ilustrar, o exemplo a seguir não *@p1* é permitido, pois é inicializado e usado na mesma instrução Declare. O exemplo a seguir apresenta um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definição de valores com SET

SET é um método comum para definir uma única variável.

As instruções a seguir sempre são maneiras válidas de definir uma variável com SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Você só pode definir uma variável por vez com SET. No entanto, os operadores compostos são permitidos.

## <a name="limitations"></a>Limitações

Você não pode usar UPDATE para atribuição de variável.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte o artigo [visão geral do desenvolvimento do Synapse SQL](develop-overview.md) .