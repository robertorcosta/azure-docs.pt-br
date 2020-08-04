---
title: 'Tutorial: Introdução à orquestração com pipelines'
description: Neste tutorial, você aprenderá a orquestrar pipelines e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e32a6a9817f2a3176e96e39c5e261875e8f4ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093160"
---
# <a name="orchestrate-with-pipelines"></a>Orquestração com pipelines

Neste tutorial, você aprenderá a orquestrar pipelines e atividades usando o Synapse Studio. 

## <a name="overview"></a>Visão geral

Você pode orquestrar uma ampla variedade de tarefas no Azure Synapse.

1. No Synapse Studio, navegue até o hub **Orquestrar**.
1. Selecione **+**  > **Pipeline** para criar um pipeline.
1. Navegue até o hub **Desenvolver** e localize o notebook que você criou anteriormente.
1. Arraste esse notebook para o pipeline.
1. No pipeline, selecione **Adicionar gatilho** > **Novo/editar**.
1. Em **Escolher gatilho**, selecione **Novo** e, em seguida, em **recorrência**, defina o gatilho para ser executado a cada hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo**. O pipeline é executado a cada hora.
1. Para fazer com que o pipeline seja executado agora sem esperar pela próxima hora, selecione **Adicionar gatilho** > **Novo/editar**.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visualizar os dados com o Power BI](get-started-visualize-power-bi.md)
                                 
