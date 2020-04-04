---
title: Configurar a importância da carga de trabalho
description: Saiba como definir a importância do nível de solicitação no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633358"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configure a importância da carga de trabalho no Azure Synapse Analytics

Definir a importância no Synapse SQL para o Azure Synapse permite que você influencie o agendamento de consultas. Consultas com maior importância serão agendadas para serem executadas antes de consultas com menor importância. Para atribuir importância às consultas, você precisa criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Crie um classificador de carga de trabalho com importância

Muitas vezes, em um cenário de data warehouse, você tem usuários que precisam de suas consultas para serem executados rapidamente.  O usuário pode ser executivos da empresa que precisam executar relatórios ou o usuário pode ser um analista executando uma consulta adhoc. Você cria um classificador de carga de trabalho para atribuir importância a uma consulta.  Os exemplos abaixo usam a nova sintaxe [de classificação de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para criar dois classificadores. `Membername`pode ser um único usuário ou um grupo. As classificações individuais dos usuários têm precedência sobre as classificações de função. Para encontrar os usuários existentes do data warehouse, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho, para um usuário com maior importância executada:

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

- Para obter mais informações sobre o gerenciamento da carga de trabalho, consulte [Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre importância, consulte [A importância da carga horária](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para gerenciar e monitorar a importância da carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
