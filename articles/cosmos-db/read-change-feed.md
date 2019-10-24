---
title: Acessando a alteração de feed no Azure Cosmos DB do Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3d30c9f946f97f06c1a3ba1cd2e77e1ab151a572
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754878"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lendo o Azure Cosmos DB alterar feed

Você pode trabalhar com o feed de alteração do Azure Cosmos DB usando qualquer uma das seguintes opções:

* Usando o Azure Functions
* Usando a biblioteca do processador de feed de alterações
* Usando a API do SQL do Azure Cosmos DB

## <a name="using-azure-functions"></a>Usando o Azure Functions

Funções do Azure é a opção mais simples e recomendada. Ao criar um gatilho de Azure Functions para Cosmos DB, você pode selecionar o contêiner a ser conectado e a função do Azure é disparada sempre que há uma alteração no contêiner. Os gatilhos podem ser criados usando o portal Funções do Azure, o portal do Azure Cosmos DB ou programaticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever as Funções do Azure e você pode até mesmo usar a CLI do Azure Functions para o desenvolvimento de plataforma cruzada. Você pode gravar e depurar o código em sua área de trabalho e, em seguida, implantar a função com um clique. Consulte [Computação de banco de dados sem servidor usando os artigos](serverless-computing-database.md) e [Usando os feeds do Azure](change-feed-functions.md) do Azure Functions para saber mais.

## <a name="using-the-change-feed-processor-library"></a>Usando a biblioteca do processador de feed de alterações

A biblioteca do processador de feed de alterações oculta a complexidade e ainda fornece um controle completo do feed de alterações. A biblioteca segue o padrão do observador, onde sua função de processamento é chamada pela biblioteca. Se você tiver feed de alterações de alta taxa de transferência, poderá instanciar vários clientes para ler o feed de alterações. Como você está usando a biblioteca do processador de feeds de mudança, ele irá dividir automaticamente a carga entre os diferentes clientes sem que você tenha que implementar essa lógica. Toda a complexidade é tratada pela biblioteca. Se você quiser ter seu próprio balanceador de carga, poderá implementar `IPartitionLoadBalancingStrategy` para uma estratégia de partição personalizada para processar o feed de alterações. Para saber mais, consulte [usando a biblioteca do processador de feeds de alterações](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Usando a API do SQL do Azure Cosmos DB

Com o SDK, você obtém um controle de baixo nível do feed de alterações. Você pode gerenciar o ponto de verificação, acessar uma chave de partição lógica específica, etc. Se você tiver vários leitores, poderá usar `ChangeFeedOptions` para distribuir a carga de leitura para diferentes threads ou clientes diferentes. 

## <a name="next-steps"></a>Próximos passos

Agora, continue sabendo mais sobre o feed de alterações conferindo os seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando o feed de alterações com o Azure Functions](change-feed-functions.md)
* [Biblioteca do processador do feed usando a alteração](change-feed-processor.md)
