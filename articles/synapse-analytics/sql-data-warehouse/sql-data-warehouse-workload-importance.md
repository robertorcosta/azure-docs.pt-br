---
title: Importância da carga de trabalho
description: Orientação para definir a importância para consultas de pool SQL sinapse no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2c8617cffaa81da6423011a494b8dbc82c42d218
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632448"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Importância da carga de trabalho do Azure Synapse Analytics

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução para solicitações de pool Synapse Synapse no Azure Synapse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

As necessidades dos negócios podem exigir que as cargas de trabalho de armazenamento de dados sejam mais importantes do que outras.  Considere um cenário onde os dados de vendas críticas da missão são carregados antes do período fiscal fechar.  Cargas de dados para outras fontes, como dados meteorológicos, não têm SLAs rigorosos. Definir alta importância para uma solicitação de carregar dados de vendas e baixa importância para uma solicitação de carregamento de dados meteorológicos garante que a carga de dados de vendas obtenha primeiro acesso aos recursos e seja concluída mais rapidamente.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixo, below_normal, normal, above_normal e alto.  As solicitações que não definem a importância são atribuídas ao nível padrão do normal. Pedidos que têm o mesmo nível de importância têm o mesmo comportamento de agendamento que existe hoje.

## <a name="importance-scenarios"></a>Cenários de importância

Além do cenário de importância básica descrito acima com vendas e dados meteorológicos, existem outros cenários em que a importância da carga de trabalho ajuda a atender às necessidades de processamento e consulta de dados.

### <a name="locking"></a>Bloqueio

O acesso aos bloqueios para atividade de leitura e gravação é uma área de contenção natural. Atividades como [comutação de partição](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [OBJETO RENAME](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) exigem bloqueios elevados.  Sem importância de carga de trabalho, o pool Synapse SQL no Azure Synapse otimiza para o throughput. Otimizar para throughput significa que, ao executar e enfileirar solicitações têm as mesmas necessidades de bloqueio e recursos estão disponíveis, as solicitações enfileiradas podem contornar solicitações com maiores necessidades de bloqueio que chegaram na fila de solicitação mais cedo. Uma vez aplicada a importância da carga de trabalho às solicitações com maiores necessidades de bloqueio. Solicitação com maior importância será executada antes da solicitação com menor importância.

Considere o exemplo a seguir:

- O Q1 está executando e selecionando ativamente dados do SalesFact.
- O Q2 está na fila esperando o Q1 ser concluído.  Ele foi enviado às 9h e está tentando dividir novos dados para SalesFact.
- O 3º trimestre é enviado às 9:01 am e deseja selecionar dados do SalesFact.

Se o Q2 e o Q3 tiverem a mesma importância e o Q1 ainda estiver sendo executado, o Q3 começará a ser executado. O 2º trimestre continuará esperando por um bloqueio exclusivo no SalesFact.  Se o Q2 tiver maior importância que o Q3, o Q3 esperará até o 2º trimestre antes de começar a execução.

### <a name="non-uniform-requests"></a>Solicitações não uniformes

Outro cenário em que a importância pode ajudar a atender às demandas de consulta é quando solicitações com diferentes classes de recursos são submetidas.  Como foi mencionado anteriormente, sob a mesma importância, a piscina Synapse SQL no Azure Synapse otimiza para o throughput. Quando as solicitações de tamanho misto (como smallrc ou mediumrc) estiverem na fila, o pool Synapse SQL escolherá a solicitação de chegada mais antiga que se encaixa nos recursos disponíveis. Se a importância da carga de trabalho for aplicada, a solicitação de maior importância será agendada em seguida.
  
Considere o seguinte exemplo em DW500c:

- Q1, Q2, Q3 e Q4 estão executando consultas smallrc.
- O Q5 é submetido com a classe de recursos do MediumRC às 9h.
- O Q6 é submetido com aula de recursos smallrc às 9:01 am.

Como o Q5 é mediumrc, requer dois slots de seleção. O Q5 precisa esperar que duas das consultas em execução se concluam.  No entanto, quando uma das consultas em execução (Q1-Q4) é concluída, o Q6 é agendado imediatamente porque os recursos existem para executar a consulta.  Se o Q5 tiver maior importância que o Q6, o Q6 espera até que o Q5 esteja em execução antes de começar a ser executado.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a criação de um classificador, consulte o [CLASSIFIER DE CARGA DE TRABALHO (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Para obter mais informações sobre a classificação da carga de trabalho, consulte [Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md).  
- Consulte o [classificador quickstart Criar carga de trabalho](quickstart-create-a-workload-classifier-tsql.md) para saber como criar um classificador de carga de trabalho.
- Confira os artigos de instruções [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [Gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) para exibir consultas e a importância atribuída.
