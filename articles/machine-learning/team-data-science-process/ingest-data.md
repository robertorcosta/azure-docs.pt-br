---
title: Carregar dados em ambientes de armazenamento do Azure - Processo de Ciência de Dados da Equipe
description: Saiba como ingerir dados em vários ambientes-alvo onde os dados são armazenados e processados.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720530"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados em ambientes de armazenamento para análise

O Processo de Ciência de Dados da Equipe exige que os dados sejam ingeridos ou carregados da maneira mais apropriada em cada etapa. Os destinos de dados podem incluir o Azure Blob Storage, bancos de dados SQL Azure, SQL Server no Azure VM, HDInsight (Hadoop), Synapse Analytics e Azure Machine Learning. 

Os artigos a seguir descrevem como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.

* Para/Do [Armazenamento de Blobs do Azure](move-azure-blob.md)
* Para [SQL Server na VM do Azure](move-sql-server-virtual-machine.md)
* Para [o banco de dados SQL do Azure](move-sql-azure.md)
* Para [Tabelas do Hive](move-hive-tables.md)
* Para [Tabelas particionadas SQL](parallel-load-sql-partitioned-tables.md)
* Do [SQL Server local](move-sql-azure-adf.md)

As necessidades técnicas e comerciais, bem como a localização, formato e tamanho inicial de seus dados determinarão o melhor plano de ingestão de dados. Não é incomum que um melhor plano tenha várias etapas. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.  A Azure Data Factory é um recurso recomendado do Azure para orquestrar a movimentação e transformação de dados.
