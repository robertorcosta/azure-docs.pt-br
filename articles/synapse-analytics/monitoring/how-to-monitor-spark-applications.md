---
title: Monitore os aplicativos do Apache Spark
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430741"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Use o Azure Synapse Studio (visualização) para monitorar seus aplicativos Apache Spark

Com o Azure Synapse Analytics, você pode usar o Spark para executar notebooks, trabalhos e outros tipos de aplicativos em seus pools spark em seu espaço de trabalho.

Este artigo explica como monitorar seus aplicativos Spark, permitindo que você fique de olho no status, problemas e progresso mais recentes.

## <a name="accessing-the-list-of-spark-applications"></a>Acessando a lista de aplicativos spark

Para ver a lista de aplicativos Spark em seu espaço de trabalho, abra primeiro [o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

  > [!div class="mx-imgBorder"]
  > ![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

  > [!div class="mx-imgBorder"]
  > ![Selecione o hub Monitor](./media/common/left-nav.png)

Selecione **aplicativos Spark** para visualizar a lista de aplicativos Spark.

  > [!div class="mx-imgBorder"]
  > ![Selecione aplicativos Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrando seus aplicativos Spark

Você pode filtrar a lista de aplicações spark para as que você está interessado. Os filtros na parte superior da tela permitem especificar um campo no qual você gostaria de filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas os aplicativos Spark que contêm o nome "vendas":

  > [!div class="mx-imgBorder"]
  > ![Botão Filtrar](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro de amostra](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visualizando detalhes sobre um aplicativo específico do Spark

Para ver os detalhes sobre uma de suas aplicações Spark, selecione o aplicativo Spark e visualize os detalhes. Se o aplicativo Spark ainda estiver em execução, você pode monitorar o progresso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento das corridas do pipeline, consulte o pipeline monitor que executa o artigo [do Azure Synapse Studio.](how-to-monitor-pipeline-runs.md)  
