---
title: Gerenciar e monitorar a importância da carga de trabalho
description: Saiba como gerenciar e monitorar a importância do nível de solicitação no Azure Synapse Analytics.
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
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350462"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Gerencie e monitore a importância da carga de trabalho no Azure Synapse Analytics

Gerencie e monitore a importância de nível de solicitação do SQL Analytics no Azure Synapse usando DMVs e visualizações de catálogo.

## <a name="monitor-importance"></a>Monitorar a importância

Monitore a importância utilizando a nova coluna de importância na visão de gestão dinâmica [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
A consulta de monitoramento abaixo mostra a hora de envio e o tempo de início das consultas. Revise o horário de envio e o horário de início, juntamente com a importância do agendamento.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para analisar mais sobre como as consultas estão sendo agendadas, use as visualizações do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerencie a importância com visualizações de catálogo

A exibição do catálogo sys.workload_management_workload_classifiers contém informações sobre classificadores. Para excluir os classificadores definidos pelo sistema que mapeiam para classes de recursos, execute o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A visualização do catálogo, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contém informações sobre os parâmetros utilizados na criação do classificador.  A consulta abaixo mostra que o ExecReportsClassifier foi criado no ```membername``` parâmetro para valores com ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![resultados da consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a má classificação, recomendamos que você remova mapeamentos de função de classe de recursos à medida que cria classificadores de carga de trabalho. O código abaixo retorna as adesões de função de classe de recursos existentes. Execute sp_droprolemember ```membername``` para cada retornado da classe de recursos correspondente.
Abaixo está um exemplo de verificação da existência antes de soltar um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre classificação, consulte [Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md).
- Para obter mais informações sobre importância, consulte [A importância da carga horária](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
