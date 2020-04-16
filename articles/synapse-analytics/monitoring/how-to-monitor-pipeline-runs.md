---
title: O pipeline do Monitor executa o Azure Synapse Studio (pré-visualização)
description: Use o Azure Synapse Studio para monitorar as operações do seu pipeline de espaço de trabalho.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430780"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Use o Azure Synapse Studio para monitorar as operações do pipeline do seu espaço de trabalho

Com o Azure Synapse Analytics, você pode criar pipelines complexos que podem automatizar e orquestrar suas atividades de movimentação de dados, transformação de dados e computação dentro de sua solução. Você pode escrever e monitorar esses pipelines usando o Azure Synapse Studio (visualização).

Este artigo explica como monitorar suas corridas de pipeline, o que permite que você fique de olho no status, problemas e progresso dos seus pipelines.

## <a name="access-the-list-of-pipeline-runs"></a>Acesse a lista de corridas de gasodutos

Para ver a lista de pipeline saqueado em seu espaço de trabalho, abra primeiro [o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecione o hub Monitor](./media/common/left-nav.png)

Selecione **pipeline executado** para exibir a lista de executões de pipeline.

![Selecione as executões do pipeline](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrando suas corridas de pipeline

Você pode filtrar a lista de corridas de gasodutos para as que você está interessado. Os filtros na parte superior da tela permitem especificar um campo no qual você gostaria de filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas o pipeline executado para o pipeline chamado "holiday":

![Botão Filtrar](./media/common/filter-button.png)

![Filtro de amostra](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visualização de detalhes sobre uma execução específica de pipeline

Para ver detalhes sobre a execução do pipeline, selecione a execução do pipeline. Em seguida, veja as atividades executadas associadas à execução do pipeline. Se o oleoduto ainda estiver funcionando, você pode monitorar o progresso. 
  
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre aplicativos de monitoramento, consulte o artigo [do Monitor Apache Spark.](how-to-monitor-spark-applications.md) 
