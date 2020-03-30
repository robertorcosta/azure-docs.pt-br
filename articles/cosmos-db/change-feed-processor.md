---
title: Alterar biblioteca do processador de ração no Azure Cosmos DB
description: Saiba como usar a biblioteca do processador de ração de alteração Azure Cosmos DB para ler o feed de alterações, os componentes do processador de feed de alteração
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273314"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB 

O processador de alimentação de mudança faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Simplifica o processo de leitura do feed de alterações e distribui o processamento do evento em vários consumidores de forma eficaz.

O principal benefício da biblioteca do processador de feed de alterações é seu comportamento tolerante a falhas que garante uma entrega "pelo menos uma vez" de todos os eventos no feed de alterações.

## <a name="components-of-the-change-feed-processor"></a>Componentes do processador de alimentação de alteração

Existem quatro componentes principais da implementação do processador de feed de alteração: 

1. **Contêiner monitorado:** o contêiner monitorado possui os dados a partir dos quais o feed de alterações é gerado. Quaisquer inserções e atualizações no recipiente monitorado são refletidas na alimentação de mudança do recipiente.

1. **O contêiner de locação:** O contêiner de locação funciona como um armazenamento estatal e coordena o processamento da alimentação de mudança em vários trabalhadores. O contêiner de locação pode ser armazenado na mesma conta que o contêiner monitorado ou em uma conta separada. 

1. **O anfitrião:** Um host é uma instância de aplicativo que usa o processador de feed de alterações para ouvir alterações. Várias instâncias com a mesma configuração de locação podem ser executadas em paralelo, mas cada instância deve ter um **nome de instância**diferente . 

1. **O delegado:** O delegado é o código que define o que você, o desenvolvedor, quer fazer com cada lote de alterações que o processador de feed de alterações lê. 

Para compreender melhor como esses quatro elementos do processador de feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. O contêiner monitorado armazena documentos e usa 'Cidade' como chave de partição. Vemos que os valores-chave de partição são distribuídos em intervalos que contêm itens. Existem duas instâncias de host e o processador de feed de alteração está atribuindo diferentes faixas de valores-chave de partição a cada instância para maximizar a distribuição de cálculo. Cada faixa está sendo lida em paralelo e seu progresso é mantido separadamente de outras faixas no contêiner de locação.

![Exemplo de processador do feed de alterações](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementação do processador de alimentação de alteração

O ponto de entrada é sempre o `Container` recipiente `GetChangeFeedProcessorBuilder`monitorado, a partir de uma instância que você chama:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Onde o primeiro parâmetro é um nome distinto que descreve o objetivo deste processador e o segundo nome é a implementação do delegado que lidará com alterações. 

Um exemplo de um delegado seria:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Finalmente você define um nome `WithInstanceName` para esta instância do processador com `WithLeaseContainer`e qual é o recipiente para manter o estado de locação com .

A `Build` chamada lhe dará a instância do `StartAsync`processador que você pode começar ligando .

## <a name="processing-life-cycle"></a>Processamento do ciclo de vida

O ciclo de vida normal de uma instância de host é:

1. Leia a ração de mudança.
1. Se não houver alterações, durma por um `WithPollInterval` período de tempo predefinido (personalizável com no Construtor) e vá para #1.
1. Se houver mudanças, envie-as ao **delegado.**
1. Quando o delegado terminar de processar as alterações **com sucesso,** atualize a loja de locação com o último ponto processado no tempo e vá para #1.

## <a name="error-handling"></a>Tratamento de erros

O processador de feed de alteração é resistente a erros de código do usuário. Isso significa que, se a implementação do seu delegado tiver uma exceção não tratada (etapa #4), o processamento de thread que o lote específico de alterações será interrompido e um novo segmento será criado. O novo segmento verificará qual foi o último ponto no tempo que a loja de locação tem para essa gama de valores-chave de partição, e reiniciará a partir daí, enviando efetivamente o mesmo lote de alterações para o delegado. Esse comportamento continuará até que seu delegado processe as alterações corretamente e é por isso que o processador de feed de alteração tem uma garantia "pelo menos uma vez", porque se o código do delegado for arremessado, ele tentará novamente esse lote.

## <a name="dynamic-scaling"></a>Escala dinâmica

Como mencionado durante a introdução, o processador de feed de alteração pode distribuir computação em várias instâncias automaticamente. Você pode implantar várias instâncias do seu aplicativo usando o processador de feed de alteração e tirar proveito dele, os únicos requisitos principais são:

1. Todas as instâncias devem ter a mesma configuração de contêiner de locação.
1. Todas as instâncias devem ter o mesmo nome de fluxo de trabalho.
1. Cada instância precisa ter um`WithInstanceName`nome de instância diferente ( ).

Se essas três condições se aplicarem, então o processador de feed de alteração irá, usando um algoritmo de distribuição igual, distribuir todas as locações no contêiner de locação em todas as instâncias em execução e paralelamente à computação. Uma locação só pode ser depropriedade de uma instância em um determinado momento, de modo que o número máximo de instâncias é igual ao número de arrendamentos.

O número de instâncias pode crescer e diminuir, e o processador de alimentação de alteração ajustará dinamicamente a carga redistribuindo de acordo.

Além disso, o processador de alimentação de alteração pode ajustar-se dinamicamente à escala de contêineres devido a aumentos de throughput ou armazenamento. Quando seu contêiner cresce, o processador de feed de alteração lida com esses cenários de forma transparente, aumentando dinamicamente as locações e distribuindo as novas locações entre as instâncias existentes.

## <a name="change-feed-and-provisioned-throughput"></a>Feed de alterações e taxa de transferência provisionada

Você é cobrado pelas RUs consumidas, pois a movimentação de dados para dentro e para fora dos contêineres do Cosmos sempre consome RUs. Você é cobrado pelas RUs consumidas pelo contêiner de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Como migrar da biblioteca do processador de feed de alterações](how-to-migrate-from-change-feed-library.md)
* [Como usar o avaliador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
