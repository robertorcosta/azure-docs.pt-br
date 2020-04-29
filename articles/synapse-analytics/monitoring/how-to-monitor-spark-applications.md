---
title: Monitorar Apache Spark aplicativos
description: Use o Azure Synapse Studio para monitorar seus aplicativos Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430741"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Use o Azure Synapse Studio (versão prévia) para monitorar seus aplicativos Apache Spark

Com o Azure Synapse Analytics, você pode usar o Spark para executar blocos de anotações, trabalhos e outros tipos de aplicativos em seus pools do Spark em seu espaço de trabalho.

Este artigo explica como monitorar seus aplicativos Spark, permitindo que você fique atento ao status, aos problemas e ao progresso mais recentes.

## <a name="accessing-the-list-of-spark-applications"></a>Acessando a lista de aplicativos Spark

Para ver a lista de aplicativos Spark em seu espaço de trabalho, primeiro [abra o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

  > [!div class="mx-imgBorder"]
  > ![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

  > [!div class="mx-imgBorder"]
  > ![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **aplicativos Spark** para exibir a lista de aplicativos Spark.

  > [!div class="mx-imgBorder"]
  > ![Selecionar aplicativos Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrando seus aplicativos Spark

Você pode filtrar a lista de aplicativos Spark para aqueles nos quais está interessado. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas os aplicativos Spark que contêm o nome "Sales":

  > [!div class="mx-imgBorder"]
  > ![Botão Filtrar](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro de exemplo](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Exibindo detalhes sobre um aplicativo Spark específico

Para exibir os detalhes sobre um dos seus aplicativos Spark, selecione o aplicativo Spark e exiba os detalhes. Se o aplicativo Spark ainda estiver em execução, você poderá monitorar o progresso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar o pipeline executa o Azure Synapse Studio](how-to-monitor-pipeline-runs.md) .  
