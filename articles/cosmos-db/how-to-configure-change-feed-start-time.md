---
title: Como configurar a hora de início do processador do feed de alterações –Azure Cosmos DB
description: Saiba como configurar o processador do feed de alterações para começar a ler de uma data e hora específicas
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: dcd92421a7ef4e2314d27a7724be01336c26c9eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018188"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Como configurar a hora de início do processador do feed de alterações

Este artigo descreve como configurar o [processador do feed de alterações](./change-feed-processor.md) para começar a ler de uma data e hora específicas.

## <a name="default-behavior"></a>Comportamento padrão

Quando um processador do feed de alterações é iniciado pela primeira vez, ele inicializa o contêiner de concessões e inicia seu [ciclo de vida de processamento](./change-feed-processor.md#processing-life-cycle). As alterações ocorridas no contêiner antes de o processador do feed de alterações ter sido inicializado pela primeira vez não serão detectadas.

## <a name="reading-from-a-previous-date-and-time"></a>Lendo de uma data e hora anteriores

É possível inicializar o processador do feed de alterações para ler as alterações de uma **data e hora específicas**, passando uma instância de um `DateTime` para a extensão do construtor `WithStartTime`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

O processador do feed de alterações será inicializado para essa data e hora específicas e começará a ler as alterações ocorridas após isso.

## <a name="reading-from-the-beginning"></a>Lendo desde o início

Em outros cenários como a migração de dados ou a análise de todo o histórico de um contêiner, precisamos ler o feed de alterações desde **o início do tempo de vida desse contêiner**. Para fazer isso, podemos usar `WithStartTime` na extensão do construtor, mas passando `DateTime.MinValue.ToUniversalTime()`, o que geraria a representação UTC do valor mínimo `DateTime`, desta forma:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

O processador do feed de alterações será inicializado e começará a ler as alterações desde o início do tempo de vida do contêiner.

> [!NOTE]
> Essas opções de personalização só funcionam para configurar o ponto de partida no tempo do processador do feed de alterações. Depois que o contêiner de concessões for inicializado pela primeira vez, alterá-las não têm nenhum efeito.

> [!NOTE]
> Ao especificar um ponto no tempo, somente as alterações para os itens que existem atualmente no contêiner serão lidas. Se um item foi excluído, seu histórico no feed de alterações também será removido.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do processador do feed de alterações](change-feed-processor.md)
* [Como usar o avaliador do feed de alterações](how-to-use-change-feed-estimator.md)
