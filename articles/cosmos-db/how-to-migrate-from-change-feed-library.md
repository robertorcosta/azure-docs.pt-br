---
title: Migre da biblioteca do processador de feed de alteração para o Azure Cosmos DB .NET V3 SDK
description: Saiba como migrar seu aplicativo usando a biblioteca do processador de feed de alterações para o Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588876"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migre da biblioteca do processador de feed de alteração para o Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de um aplicativo existente que usa a biblioteca do [processador de feed de alteração](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) para o recurso de feed de [alterações](change-feed.md) na versão mais recente do .NET SDK (também conhecido como .NET V3 SDK).

## <a name="required-code-changes"></a>Mudanças de código necessárias

O .NET V3 SDK tem várias alterações de ruptura, as seguintes são as principais etapas para migrar seu aplicativo:

1. Converta `DocumentCollectionInfo` as `Container` instâncias em referências para os contêineres monitorados e alugados.
1. As personalizações `WithProcessorOptions` que utilizam `WithLeaseConfiguration` devem `WithPollInterval` ser atualizadas para uso e intervalos, `WithStartTime` [para o horário de início](how-to-configure-change-feed-start-time.md)e `WithMaxItems` para definir a contagem máxima de itens.
1. Defina `processorName` `GetChangeFeedProcessorBuilder` o 'para corresponder `ChangeFeedProcessorOptions.LeasePrefix`ao valor `string.Empty` configurado' ou use de outra forma.
1. As alterações não são `IReadOnlyList<Document>`mais entregues como um `IReadOnlyCollection<T>` `T` , em vez disso, é um onde é um tipo que você precisa definir, não há mais classe de item base.
1. Para lidar com as alterações, você não precisa mais de uma implementação, em vez disso, você precisa [definir um delegado](change-feed-processor.md#implementing-the-change-feed-processor). O delegado pode ser uma função estática ou, se você precisar manter o estado entre as execuções, você pode criar sua própria classe e passar um método de instância como delegado.

Por exemplo, se o código original para construir o processador de feed de alteração for o seguinte:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

O código migrado se parecerá com:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E o delegado pode ser um método estático:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Estado e contêiner de locação

Semelhante à biblioteca do processador de feed de alteração, o recurso de feed de alteração no .NET V3 SDK usa um [contêiner de locação](change-feed-processor.md#components-of-the-change-feed-processor) para armazenar o estado. No entanto, os esquemas são diferentes.

O processador de feed de alteração SDK V3 detectará qualquer estado de biblioteca antigo e migrará-o para o novo esquema automaticamente após a primeira execução do código de aplicativo migrado. 

Você pode parar o aplicativo com segurança usando o código antigo, migrar o código para a nova versão, iniciar o aplicativo migrado e quaisquer alterações que aconteceram enquanto o aplicativo foi interrompido, serão pegos e processados pela nova versão.

> [!NOTE]
> As migrações de aplicativos usando a biblioteca para o .NET V3 SDK são unidirecionais, uma vez que o estado (arrendamentos) será migrado para o novo esquema. A migração não é compatível com o retrocesso.


## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do processador do feed de alterações](change-feed-processor.md)
* [Como usar o avaliador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
