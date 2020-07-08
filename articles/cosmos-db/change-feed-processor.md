---
title: Processador do feed de alterações no Azure Cosmos DB
description: Saiba como usar o processador do feed de alterações do Azure Cosmos DB para ler o feed de alterações, os componentes do processador do feed de alterações
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.openlocfilehash: 4325f75ac8181e088d64e53d3f65e085a09c0224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85119402"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB

O processador do feed de alterações faz parte do [SDK V3 do Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3). Ele simplifica o processo de leitura do feed de alterações e distribui o processamento de eventos entre vários consumidores com eficiência.

O principal benefício da biblioteca do processador do feed de alterações é seu comportamento tolerante a falhas, que garante uma entrega "pelo menos uma vez" de todos os eventos no feed de alterações.

## <a name="components-of-the-change-feed-processor"></a>Componentes do processador do feed de alterações

Há quatro componentes principais de implementação do processador de feed de alterações:

1. **Contêiner monitorado:** O contêiner monitorado possui os dados a partir dos quais o feed de alterações é gerado. Todas as inserções e atualizações no contêiner monitorado são refletidas no feed de alterações do contêiner.

1. **Contêiner de concessão:** O contêiner de concessão atua como um armazenamento de estado e coordena o processamento do feed de alterações em vários trabalhos. O contêiner de concessão pode ser armazenado na mesma conta que o contêiner monitorado ou em uma conta separada.

1. **O host:** Um host é uma instância de aplicativo que usa o processador do feed de alterações para escutar alterações. Várias instâncias com a mesma configuração de concessão podem ser executadas em paralelo, mas cada instância deve ter um **nome de instância** diferente.

1. **O delegado:** O delegado é o código que define o que você, o desenvolvedor, deseja fazer com cada lote de alterações lido pelo processador do feed de alterações. 

Para compreender melhor como esses quatro elementos do processador de feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. O contêiner monitorado armazena documentos e usa 'Cidade' como chave de partição. Vemos que os valores da chave de partição são distribuídos em intervalos que contêm itens. Há duas instâncias de host e o processador do feed de alterações está atribuindo diferentes intervalos de valores da chave de partição a cada instância para maximizar a distribuição de computação. Cada intervalo está sendo lido em paralelo e seu progresso é mantido separadamente de outros intervalos no contêiner de concessão.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Exemplo de processador do feed de alterações" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementar o processador do feed de alterações

O ponto de entrada é sempre o contêiner monitorado, de uma instância `Container` chamada `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Onde o primeiro parâmetro é um nome distinto que descreve a meta desse processador e o segundo nome é a implementação delegada que manipulará as alterações. 

Um exemplo de uma delegação seria:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Finalmente, você define um nome para essa instância de processador com `WithInstanceName` e o contêiner para manter o estado de concessão com `WithLeaseContainer`.

Chamar `Build` fornecerá a instância do processador que você pode iniciar chamando `StartAsync`.

## <a name="processing-life-cycle"></a>Ciclo de vida do processamento

O ciclo de vida normal de uma instância de host é:

1. Ler o feed de alterações.
1. Se não houver alteração, suspenda por um período de tempo predefinido (personalizável com `WithPollInterval` no Construtor) e vá para 1.
1. Se houver alterações, envie-as para o **delegado**.
1. Quando o delegado terminar de processar as alterações **com êxito**, atualize o repositório de concessão com o último ponto processado no tempo e vá para 1.

## <a name="error-handling"></a>Tratamento de erros

O processador do feed de alterações é resiliente aos erros de código do usuário. Isso significa que, se sua implementação delegada tiver uma exceção sem tratamento (etapa 4), o processamento de threads que o lote de alterações específico será interrompido e um novo thread será criado. O novo thread verificará qual foi o último ponto no tempo que o repositório de concessão tem para esse intervalo de valores de chave de partição e reiniciará a partir de lá, enviando efetivamente o mesmo lote de alterações para o delegado. Esse comportamento continuará até que o delegado processe as alterações corretamente e seja o motivo pelo qual o processador do feed de alterações tem uma garantia "pelo menos uma vez", porque se o código delegado lançar uma exceção, ele tentará novamente esse lote.

Para impedir que o processador do feed de alterações fique "preso" repetindo continuamente o mesmo lote de alterações, adicione lógica ao código delegado para gravar documentos, mediante exceção, em uma fila de mensagens mortas. Esse design garante o controle das alterações não processadas enquanto ainda pode continuar a processar alterações futuras. A fila de mensagens mortas pode ser simplesmente outro contêiner do Cosmos. O armazenamento de dados exato não importa, basta que as alterações não processadas sejam persistidas.

Além disso, use o [avaliador do feed de alterações](how-to-use-change-feed-estimator.md) para monitorar o progresso das instâncias do processador do feed de alterações ao ler o feed. Além de monitorar se o processador do feed de alterações fica "preso" repetindo continuamente o mesmo lote de alterações, também é possível entender se o processador do feed de alterações está atrasado devido a recursos disponíveis, como CPU, memória e largura de banda da rede.

## <a name="deployment-unit"></a>Unidade de implantação

Uma única unidade de implantação do processador do feed de alterações consiste em uma ou mais instâncias com a mesma `processorName` e configuração de contêiner de concessão. Você pode ter várias unidades de implantação em que cada uma tem um fluxo de negócios diferente para as alterações e cada unidade de implantação que consiste em uma ou mais instâncias. 

Por exemplo, você pode ter uma unidade de implantação que aciona uma API externa sempre que houver uma alteração no contêiner. Outra unidade de implantação pode mover dados em tempo real, cada vez que houver uma alteração. Quando ocorrer uma alteração no contêiner monitorado, todas as unidades de implantação serão notificadas.

## <a name="dynamic-scaling"></a>Escala dinâmica

Como mencionado anteriormente, em uma unidade de implantação, você pode ter uma ou mais instâncias. Para aproveitar a distribuição de computação na unidade de implantação, os únicos requisitos principais são:

1. Todas as instâncias devem ter a mesma configuração do contêiner de concessão.
1. Todas as instâncias devem ter o mesmo `processorName`.
1. Cada instância precisa ter um nome de instância diferente (`WithInstanceName`).

Se essas três condições se aplicarem, o processador do feed de alterações distribuirá, usando um algoritmo de distribuição igual, todas as concessões no contêiner de concessão em todas as instâncias em execução dessa unidade de implantação e paralelizará a computação. Uma concessão só pode pertencer a uma instância em um determinado momento, portanto, a quantidade máxima de instâncias é igual ao número de concessões.

A quantidade de instâncias pode aumentar e diminuir, e o processador do feed de alterações ajustará dinamicamente a carga redistribuindo de acordo.

Além disso, o processador do feed de alterações pode se ajustar dinamicamente à escala dos contêineres devido a aumentos na taxa de transferência ou no armazenamento. Quando o contêiner cresce, o processador do feed de alterações manipula esses cenários de forma transparente, aumentando dinamicamente as concessões e distribuindo as novas concessões entre as instâncias existentes.

## <a name="change-feed-and-provisioned-throughput"></a>Feed de alterações e taxa de transferência provisionada

Você é cobrado pelas RUs consumidas, pois a movimentação de dados para dentro e para fora dos contêineres do Cosmos sempre consome RUs. Você é cobrado pelas RUs consumidas pelo contêiner de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Modelo de pull do feed de alterações](change-feed-pull-model.md)
* [Como migrar da biblioteca do processador do feed de alterações](how-to-migrate-from-change-feed-library.md)
* [Como usar o avaliador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
