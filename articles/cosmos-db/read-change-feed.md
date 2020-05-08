---
title: Acessando a alteração de feed no Azure Cosmos DB do Azure Cosmos DB
description: Este artigo descreve as diferentes opções disponíveis para ler e acessar o feed de alterações no Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982489"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lendo o Azure Cosmos DB alterar feed

Você pode trabalhar com o feed de alteração do Azure Cosmos DB usando qualquer uma das seguintes opções:

* Usando o Azure Functions
* Usando o processador do feed de alterações
* Usando a API do SQL do Azure Cosmos DB
* Usando o modelo de pull do feed de alterações (visualização)

## <a name="using-azure-functions"></a>Usando o Azure Functions

Funções do Azure é a opção mais simples e recomendada. Ao criar um gatilho de Azure Functions para Cosmos DB, você pode selecionar o contêiner a ser conectado e a função do Azure é disparada sempre que há uma alteração no contêiner. Os gatilhos podem ser criados usando o portal Funções do Azure, o portal do Azure Cosmos DB ou programaticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever as Funções do Azure e você pode até mesmo usar a CLI do Azure Functions para o desenvolvimento de plataforma cruzada. Você pode gravar e depurar o código em sua área de trabalho e, em seguida, implantar a função com um clique. Consulte [Computação de banco de dados sem servidor usando os artigos](serverless-computing-database.md) e [Usando os feeds do Azure](change-feed-functions.md) do Azure Functions para saber mais.

## <a name="using-the-change-feed-processor"></a>Usando o processador do feed de alterações

O processador do feed de alterações oculta a complexidade e ainda oferece um controle completo do feed de alterações. A biblioteca segue o padrão do observador, onde sua função de processamento é chamada pela biblioteca. Se você tiver feed de alterações de alta taxa de transferência, poderá instanciar vários clientes para ler o feed de alterações. Como você está usando a biblioteca do processador de feeds de mudança, ele irá dividir automaticamente a carga entre os diferentes clientes sem que você tenha que implementar essa lógica. Toda a complexidade é tratada pela biblioteca. Para saber mais, consulte [usando o processador do feed de alterações](change-feed-processor.md). O processador do feed de alterações faz parte do [SDK do Azure Cosmos DB v3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Usando a API do SQL do Azure Cosmos DB

Com o SDK, você obtém um controle de baixo nível do feed de alterações. Você pode gerenciar o ponto de verificação, acessar uma chave de partição lógica específica, etc. Se você tiver vários leitores, poderá usar `ChangeFeedOptions` o para distribuir a carga de leitura para diferentes threads ou clientes diferentes.

## <a name="using-the-change-feed-pull-model"></a>Usando o modelo de pull do feed de alterações

O [modelo de pull do feed de alterações](change-feed-pull-model.md) permite que você consuma o feed de alterações em seu próprio ritmo e paraleliza o processamento de alterações com o FeedRanges. Um FeedRange abrange um intervalo de valores de chave de partição. Usando o modelo de pull do feed de alterações, também é fácil processar alterações para uma chave de partição específica.

> [!NOTE]
> O modelo de pull do feed de alterações está atualmente em [visualização somente no SDK Azure Cosmos DB .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . A versão prévia ainda não está disponível para outras versões do SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade do feed de alterações é inserida como fluxo de alteração na API do MongoDB e consulta com predicado em API do Cassandra. Para saber mais sobre os detalhes de implementação para a API do MongoDB, consulte os [fluxos de alteração na API de Azure Cosmos DB para MongoDB](mongodb-change-streams.md).

O Apache Cassandra nativo fornece a captura de dados de alteração (CDC), um mecanismo para sinalizar tabelas específicas para arquivamento, bem como rejeitar gravações para essas tabelas assim que um tamanho configurável no disco para o log CDC for atingido. O recurso de feed de alterações na API Azure Cosmos DB para Cassandra aprimora a capacidade de consultar as alterações com o predicado por meio de CQL. Para saber mais sobre os detalhes da implementação, consulte [o feed de alterações na API de Azure Cosmos DB para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando a biblioteca do processador do feed de alterações](change-feed-processor.md)
