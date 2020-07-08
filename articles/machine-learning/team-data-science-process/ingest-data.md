---
title: Carregar dados em ambientes de armazenamento do Azure-processo de ciência de dados de equipe
description: Saiba mais sobre como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76720530"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados em ambientes de armazenamento para análise

O processo de ciência de dados de equipe exige que os dados sejam ingeridos ou carregados na maneira mais apropriada em cada estágio. Os destinos de dados podem incluir o armazenamento de BLOBs do Azure, os bancos de SQL Azure, SQL Server na VM do Azure, HDInsight (Hadoop), Synapse Analytics e Azure Machine Learning. 

Os artigos a seguir descrevem como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.

* Para/Do [Armazenamento de Blobs do Azure](move-azure-blob.md)
* Para [SQL Server na VM do Azure](move-sql-server-virtual-machine.md)
* Para o [banco de dados SQL do Azure](move-sql-azure.md)
* Para [Tabelas do Hive](move-hive-tables.md)
* Para [Tabelas particionadas SQL](parallel-load-sql-partitioned-tables.md)
* Do [SQL Server local](move-sql-azure-adf.md)

As necessidades técnicas e de negócios, bem como o local inicial, o formato e o tamanho dos dados, determinarão o melhor plano de ingestão de dados. Não é incomum que um plano melhor tenha várias etapas. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.  Azure Data Factory é um recurso recomendado do Azure para orquestrar a movimentação e a transformação de dados.
