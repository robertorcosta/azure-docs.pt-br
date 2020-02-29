---
title: Configurar a importância da carga de trabalho
description: Saiba como definir a importância do nível de solicitação no Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196790"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurar a importância da carga de trabalho no Azure Synapse Analytics

A definição da importância na análise de SQL para o Azure Synapse permite que você influencie o agendamento de consultas. As consultas com maior importância serão agendadas para serem executadas antes das consultas com menor importância. Para atribuir importância a consultas, você precisa criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um classificador de carga de trabalho com importância

Geralmente, em um cenário de data warehouse, você tem usuários que precisam de suas consultas para serem executados rapidamente.  O usuário poderia ser executivos da empresa que precisam executar relatórios ou o usuário pode ser um analista executando uma consulta adhoc. Você cria um classificador de carga de trabalho para atribuir importância a uma consulta.  Os exemplos a seguir usam a nova sintaxe [criar classificação de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) para criar dois classificadores.  MemberName pode ser um único usuário ou um grupo. As classificações de usuário individuais têm precedência sobre as classificações de função. Para localizar usuários existentes do data warehouse, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um usuário com maior importância de execução:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Para criar um classificador de carga de trabalho para um usuário executando consultas adhoc com menor importância de execução:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre o gerenciamento de carga de trabalho, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para gerenciar e monitorar a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
