---
title: 'Tutorial: Introdução à orquestração com pipelines'
description: Neste tutorial, você aprenderá a orquestrar pipelines e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329876"
---
# <a name="orchestrate-with-pipelines"></a>Orquestração com pipelines

Neste tutorial, você aprenderá a orquestrar pipelines e atividades usando o Synapse Studio. 

## <a name="overview"></a>Visão geral

Você pode orquestrar uma ampla variedade de tarefas no Azure Synapse.

1. No Synapse Studio, acesse o hub **Integrar** .
1. Selecione **+**  > **Pipeline** para criar um pipeline.
1. Acesse o hub **Desenvolver** e selecione um dos notebooks que você criou anteriormente.
1. Arraste esse notebook para o pipeline ( **Observação** : adicione a etapa importar módulos no notebook conforme especificado em [documento](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) que são necessárias durante a execução do pipeline)
1. No pipeline, selecione **Adicionar gatilho** > **Novo/editar** .
1. Em **Escolher gatilho** , selecione **Novo** e defina **Recorrência** como "a cada 1 hora".
1. Selecione **OK** . 
1. Selecione **Publicar Tudo** .
1. Para fazer o pipeline ser executado imediatamente, sem esperar pela próxima hora, selecione **Adicionar gatilho** > **Disparar agora** .



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visualizar os dados com o Power BI](get-started-visualize-power-bi.md)
                                 
