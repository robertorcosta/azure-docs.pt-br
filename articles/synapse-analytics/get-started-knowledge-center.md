---
title: 'Tutorial: Comece a explorar o Centro de Conhecimento do Azure Synapse'
description: Neste tutorial, você aprenderá a usar o Centro de Conhecimento do Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 57213e228359fd6c201d501d3bb75f7267054277
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685419"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explorar o Centro de Conhecimento do Azure Synapse

Neste tutorial, você aprenderá a usar o Centro de Conhecimento do Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Introdução ao Centro de Conhecimento

Há duas maneiras de localizar o Centro de Conhecimento no Synapse Studio:

  1. No hub inicial, ao lado da parte superior da página, clique em **Learn**.
  2. Na barra de menus na parte superior, clique em **?** e, em seguida,  em **Centro de Conhecimento**.

Escolha um dos métodos e abra o **Centro de Conhecimento**.

## <a name="overview"></a>Visão geral

O **Centro de Conhecimento** permite que você faça três coisas:
* **Use amostras imediatamente**. Essa opção é otimizada para você ver a análise em ação o mais rápido possível. Se você quiser um exemplo rápido de como o Azure Synapse funciona, escolha essa opção.
* **Amostra de navegador disponível**. Essa opção permite vincular conjuntos de dados de exemplo e adicionar código de exemplo na forma de scripts SQL, notebooks e pipelines.
* **Fazer um tour no Synapse studio**. Essa opção o levará em um breve tour pelas partes básicas do Synapse Studio. Isso será útil se você nunca tiver usado o Synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Como explorar o armazenamento de blobs com o pool de SQL sem servidor

1. Acesse o **Centro de Conhecimento**, clique em **Usar exemplos imediatamente**
1. Selecione **Consultar dados com SQL** 
1. Clique em **Usar amostras imediatamente**
1. Isso criará um novo script SQL.
1. Role até a primeira consulta (linhas 28 a 32) e selecione o texto da consulta
1. Clique em Executar. Isso executará o texto selecionado.

## <a name="loading-more-nyc-taxi-data"></a>Como carregar mais dados de táxi de NYC
1. Acesse o **Centro de Conhecimento** e clique em **Procurar exemplos disponíveis** 
1. Selecione a guia **Scripts SQL** na parte superior
1. Selecione **Carregar o conjunto de dados de táxis de Nova York**
1. Em **Entradas**, escolha **Selecionar um pool existente** e selecione **SQLDB1**
1. Clique **Abrir Script**
1. Um novo script SQL será exibido.
1. Clique em **Executar**
1. Isso criará várias tabelas para todos os dados de táxi de NYC e as carregará usando o comando COPY do T-SQL.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)
