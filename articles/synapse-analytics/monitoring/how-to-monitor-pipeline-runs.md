---
title: Monitorar execuções de pipeline usando o Synapse Studio
description: Use o Synapse Studio para monitorar as execuções de pipeline de espaço de trabalho.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 385b06dd7756c3bebf6557f9ea0b518473a37d49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075905"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Usar o Synapse Studio para monitorar as execuções de pipeline de espaço de trabalho

Com o Azure Synapse Analytics, você pode criar pipelines complexos que podem automatizar e orquestrar a movimentação de dados, a transformação de dados e as atividades de computação em sua solução. Você pode criar e monitorar esses pipelines usando o Synapse Studio (versão prévia).

Este artigo explica como monitorar as execuções de pipeline, o que permite que você fique atento ao status, aos problemas e ao progresso mais recentes de seus pipelines.

## <a name="access-the-list-of-pipeline-runs"></a>Acessar a lista de execuções de pipeline

Para ver a lista de execuções de pipeline em seu espaço de trabalho, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **pipeline executions** para exibir a lista de execuções de pipeline.

![Selecionar execuções de pipeline](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrando suas execuções de pipeline

Você pode filtrar a lista de execuções de pipeline para aquelas nas quais você está interessado. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas as execuções de pipeline para o pipeline chamado "feriado":

![Botão Filtrar](./media/common/filter-button.png)

![Filtro de exemplo](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Exibindo detalhes sobre uma execução de pipeline específica

Para exibir detalhes sobre a execução do pipeline, selecione a execução do pipeline. Em seguida, exiba as execuções de atividade associadas à execução do pipeline. Se o pipeline ainda estiver em execução, você poderá monitorar o progresso. 
  
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento de aplicativos, consulte o artigo [monitorar Apache Spark aplicativos](how-to-monitor-spark-applications.md) . 
