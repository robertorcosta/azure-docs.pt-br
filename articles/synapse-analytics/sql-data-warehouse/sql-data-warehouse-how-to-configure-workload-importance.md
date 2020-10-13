---
title: Configurar a importância da carga de trabalho
description: Saiba como definir a importância do nível de solicitação no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212115"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurar a importância no Azure Synapse Analytics

Definir a importância no SQL do Synapse no Azure Synapse possibilita influenciar o agendamento de consultas. As consultas com importância mais alta serão agendadas para serem executadas antes das queries com importância mais baixa. Para atribuir importância às consultas, é necessário criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um Classificador de Carga de Trabalho com Importância

Normalmente, em um cenário de data warehouse, há usuários em um sistema ocupado que precisam realizar consultas com rapidez.  O usuário pode ser um executivo da empresa que precisa fazer relatórios ou um analista fazendo uma consulta adhoc. Para atribuir importância, você cria um classificador de carga de trabalho e a importância é atribuída a uma consulta.  Os exemplos abaixo usam a sintaxe [criar classificador de força de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para criar dois classificadores. `Membername` pode ser um único usuário ou um grupo.  Para encontrar usuários existentes do data warehouse, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho para um usuário com importância mais alta, execute:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Para criar um classificador de carga de trabalho para um usuário realizando consultas adhoc com importância mais baixa, execute:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o gerenciamento de carga de trabalho, consulte [Classificação de Carga de Trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre Importância, consulte [Importância de Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Acesse Gerenciar e monitorar a Importância da Carga de Trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
