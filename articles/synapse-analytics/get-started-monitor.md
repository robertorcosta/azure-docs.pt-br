---
title: 'Tutorial: Introdução ao Azure Synapse Analytics – monitorar seu workspace do Synapse'
description: Neste tutorial, você aprenderá a monitorar atividades no workspace do Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708165"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorar o workspace do Synapse

Neste tutorial, você aprenderá a monitorar atividades no workspace do Synapse. Você pode monitorar as atividades atuais e históricas do SQL, Apache Spark. e de Pipelines. 

## <a name="introduction-to-the-monitor-hub"></a>Introdução ao Hub de monitoramento

Abra o Synapse Studio e navegue até o hub de **Monitoramento**. Aqui você poderá ver um histórico de todas as atividades que estão ocorrendo no workspace e quais estão ativas no momento. 

* Em **Orquestração**, você pode monitorar pipelines, gatilhos e Runtimes de integração
* Em **Atividades**, você pode monitorar atividades do Spark e do SQL. 

## <a name="orchestration"></a>Orquestração

1. Navegue até **Orquestração > Pipeline**. Nessa exibição, você pode ver toda vez que um pipeline é executado em seu workspace. 
1. Localize o pipeline que você executou na etapa anterior e clique no respectivo **Nome de pipeline**.
1. Agora você pode ver como as atividades individuais dentro desse pipeline são executadas.
1. Clique em **Barra de trilha** perto da parte superior do Synapse Studio, clique em **Todas as execuções de pipeline** para retornar à exibição anterior.

## <a name="apache-spark-activities"></a>Atividades do Apache Spark

1. Navegue até **Orquestração > Atividades > Aplicativos Apache Spark**. Agora você pode ver todos os aplicativos Spark em execução ou que foram executados em seu workspace.
1. Localize um aplicativo que não esteja mais em execução e clique em **Nome de aplicativo**. Agora você pode ver os detalhes do aplicativo Spark.
1. Se você estiver familiarizado com Apache Spark, poderá encontrar a interface do usuário do servidor de histórico do Apache Spark padrão clicando em **Servidor de histórico do Spark**.

## <a name="sql-activities"></a>Atividades do SQL

1. Navegue até **Orquestração > Atividades > Solicitações SQL**.
1. Nesta exibição, você pode ver solicitações SQL.
1. Selecione um **Pool** para monitorar. Agora você pode ver todas as solicitações SQL que estão em execução ou foram executadas em seu workspace nesse pool.
1. Localize uma solicitação SQL específica e passe o mouse sobre esse item. Depois de passar o mouse, você verá um ícone de script SQL exibido.
1. Clique no ícone de script SQL para ver o texto completo da solicitação SQL.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar o Centro de Conhecimento](get-started-knowledge-center.md)
