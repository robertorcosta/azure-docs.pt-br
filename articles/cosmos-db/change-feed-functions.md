---
title: Como usar o feed de alterações do Azure Cosmos DB com o Azure Functions
description: Use funções do Azure para conectar-se ao feed de alterações do Azure Cosmos DB. Mais tarde, você pode criar funções azure reativas que são acionadas em cada novo evento.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851374"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas baseadas em eventos sem servidor com funções Azure Cosmos DB e Azure

As funções do Azure fornecem a maneira mais simples de se conectar ao [feed de alteração](change-feed.md). Você pode criar pequenas funções reativas do Azure que serão acionadas automaticamente em cada novo evento no feed de alterações do seu contêiner Azure Cosmos.

![Funções baseadas em eventos sem servidor trabalhando com o gatilho Funções do Azure para O Cosmos DB](./media/change-feed-functions/functions.png)

Com o [gatilho Funções do Azure para Cosmos DB,](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)você pode aproveitar a funcionalidade de dimensionamento e detecção de eventos confiáveis do [Processador de Ração](./change-feed-processor.md)de Alteração sem a necessidade de manter qualquer [infra-estrutura do trabalhador.](./change-feed-processor.md) Concentre-se na lógica da sua função Azure sem se preocupar com o resto do pipeline de fornecimento de eventos. Você pode até mesmo misturar o Gatilho com quaisquer [outras vinculações de funções do Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho Funções Do Azure para Cosmos DB é suportado apenas para uso com a API Core (SQL).

## <a name="requirements"></a>Requisitos

Para implementar um fluxo baseado em eventos sem servidor, você precisa:

* **O contêiner monitorado**: O contêiner monitorado é o contêiner Azure Cosmos que está sendo monitorado, e armazena os dados dos quais a ração de mudança é gerada. Quaisquer inserções, atualizações no recipiente monitorado são refletidas na alimentação de mudança do recipiente.
* **O contêiner de locação**: O contêiner de locação mantém o estado em várias e dinâmicas instâncias de função Azure sem servidor e permite o dimensionamento dinâmico. Este contêiner de locação pode ser criado manualmente ou automaticamente pelo gatilho Funções Do Azure para Cosmos DB. Para criar automaticamente o contêiner de locação, defina o sinalizador *CreateLeaseCollectionIfNotExiste* na [configuração](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Os contêineres de locação `/id` particionada são necessários para ter uma definição de chave de partição.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Crie o gatilho de funções do Azure para o Cosmos DB

A criação da função Azure com um gatilho de funções do Azure para o Cosmos DB agora é suportada em todas as integrações IDE e CLI do Azure Functions:

* [Visual Studio Extension](../azure-functions/functions-develop-vs.md) para usuários do Visual Studio.
* [Visual Studio Code Extension](/azure/javascript/tutorial-vscode-serverless-node-01) para usuários do Visual Studio Code.
* E, finalmente, [core CLI ferramentapara](../azure-functions/functions-run-local.md#create-func) uma experiência agnóstica IDE multiplataforma.

## <a name="run-your-trigger-locally"></a>Execute seu gatilho localmente

Você pode executar sua [função Azure localmente](../azure-functions/functions-develop-local.md) com o [Azure Cosmos DB Emmulator](./local-emulator.md) para criar e desenvolver seus fluxos baseados em eventos sem servidor sem uma assinatura do Azure ou incorrendo em quaisquer custos.

Se você quiser testar cenários ao vivo na nuvem, você pode experimentar o [Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou assinatura do Azure necessária.

## <a name="next-steps"></a>Próximas etapas

Agora você pode continuar a aprender mais sobre feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando a biblioteca do processador de feed de alterações](change-feed-processor.md)
* [Como trabalhar com a biblioteca do processador de feed de alterações](change-feed-processor.md)
* [Computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions](serverless-computing-database.md)
