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
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667487"
---
# <a name="orchestrate-with-pipelines"></a>Orquestração com pipelines

Neste tutorial, você aprenderá a orquestrar pipelines e atividades usando o Synapse Studio. 

## <a name="overview"></a>Visão geral

Você pode orquestrar uma ampla variedade de tarefas no Azure Synapse.

1. No Synapse Studio, navegue até o hub **Orquestrar**.
1. Selecione **+**  > **Pipeline** para criar um pipeline.
1. Acesse o hub **Desenvolver** e selecione um dos notebooks que você criou anteriormente.
1. Arraste esse notebook para o pipeline.
1. No pipeline, selecione **Adicionar gatilho** > **Novo/editar**.
1. Em **Escolher gatilho**, selecione **Novo** e, em seguida, em **recorrência**, defina o gatilho para ser executado a cada hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo**. O pipeline é executado a cada hora.
1. Para fazer com que o pipeline seja executado agora sem esperar pela próxima hora, selecione **Adicionar gatilho** > **Novo/editar**.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visualizar os dados com o Power BI](get-started-visualize-power-bi.md)
                                 
