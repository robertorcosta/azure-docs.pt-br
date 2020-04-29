---
title: Monitorar execuções de pipeline do Azure Synapse Studio (visualização)
description: Use o Azure Synapse Studio para monitorar as execuções de pipeline de espaço de trabalho.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430780"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Usar o Azure Synapse Studio para monitorar as execuções de pipeline de espaço de trabalho

Com o Azure Synapse Analytics, você pode criar pipelines complexos que podem automatizar e orquestrar a movimentação de dados, a transformação de dados e as atividades de computação em sua solução. Você pode criar e monitorar esses pipelines usando o Azure Synapse Studio (versão prévia).

Este artigo explica como monitorar as execuções de pipeline, o que permite que você fique atento ao status, aos problemas e ao progresso mais recentes de seus pipelines.

## <a name="access-the-list-of-pipeline-runs"></a>Acessar a lista de execuções de pipeline

Para ver a lista de execuções de pipeline em seu espaço de trabalho, primeiro [abra o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

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
