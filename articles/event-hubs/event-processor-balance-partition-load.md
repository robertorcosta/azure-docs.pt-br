---
title: Equilibrar a carga de partição em várias instâncias - Azure Event Hubs | Microsoft Docs
description: Descreve como equilibrar a carga de partição em várias instâncias do seu aplicativo usando um processador de eventos e o SDK do Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398306"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Equilibrar a carga de partição em várias instâncias do seu aplicativo
Para dimensionar seu aplicativo de processamento de eventos, você pode executar várias instâncias do aplicativo e fazê-lo equilibrar a carga entre si. Nas versões mais antigas, [eventProcessorHost](event-hubs-event-processor-host.md) permitiu equilibrar a carga entre várias instâncias do programa e eventos de ponto de verificação ao receber. Nas versões mais recentes (5.0 em diante), **EventProcessorClient** (.NET e Java), ou **EventHubConsumerClient** (Python e JavaScript) permite que você faça o mesmo. O modelo de desenvolvimento é mais simples usando eventos. Você se inscreve nos eventos em que está interessado, registrando um manipulador de eventos.

Este artigo descreve um cenário de exemplo para usar várias instâncias para ler eventos de um hub de eventos e, em seguida, dar-lhe detalhes sobre recursos do cliente processador de eventos, que permite que você receba eventos de várias partições ao mesmo tempo e carregue o equilíbrio com outros consumidores que usam o mesmo hub de eventos e grupo de consumidores.

> [!NOTE]
> A chave para reduzir os Hubs de Eventos horizontalmente é o modelo de consumidor particionado. Em contraste com o [consumidores concorrentes](https://msdn.microsoft.com/library/dn568101.aspx) padrão, o padrão de consumidor particionado permite alta escala, removendo o gargalo de contenção e promovendo o paralelismo de ponta a ponta.

## <a name="example-scenario"></a>Cenário de exemplo

Como um cenário de exemplo, considere uma empresa de segurança inicial que monitora 100.000 residências. A cada minuto, ele obtém dados de vários sensores, como um detector de movimento, sensor aberto de porta/janela, detector de quebra de vidro, e assim por diante, instalado em cada casa. A empresa oferece um site da web para residentes monitorarem a atividade da sua casa quase em tempo real.

Cada sensor envia dados para um hub de eventos. O hub de eventos é configurado com 16 partições. Na extremidade consumidora, você precisa de um mecanismo que possa ler esses eventos, consolidá-los (filtrar, agregar e assim por diante) e despejar o agregado para uma bolha de armazenamento, que é então projetada para uma página web fácil de usar.

## <a name="write-the-consumer-application"></a>Escrever o aplicativo de consumidor

Ao projetar o consumidor em um ambiente distribuído, o cenário deve lidar com os seguintes requisitos:

1. **Escala:** criar vários consumidores, com cada consumidor assumindo a propriedade de leitura de algumas partições de Hubs de eventos.
2. **O balanceamento de carga:** aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detector de monóxido de carbono) é adicionado a cada página inicial, aumenta o número de eventos. Nesse caso, o operador (um ser humano) aumenta o número de instâncias de consumidor. Em seguida, o pool de consumidores pode redistribuir o número de partições que são proprietárias, para compartilhar a carga com os consumidores recém-adicionados.
3. **Currículo perfeito sobre falhas:** Se um consumidor (**consumidor A**) falhar (por exemplo, a máquina virtual que hospeda o consumidor de repente falha), então outros consumidores podem pegar as partições de propriedade do **consumidor A** e continuar. Além disso, o ponto de continuação, chamado de *checkpoint* ou *offset*, deve estar no ponto exato em que **consumidor A** falhou, ou um pouco antes disso.
4. **Eventos de consumo:** Enquanto os três pontos anteriores tratam da gestão do consumidor, deve haver código para consumir os eventos e fazer algo útil com ele. Por exemplo, agregá-lo e carregá-lo para o armazenamento blob.

## <a name="event-processor-or-consumer-client"></a>Processador de eventos ou cliente consumidor

Você não precisa construir sua própria solução para atender a esses requisitos. Os SDKs do Azure Event Hubs fornecem essa funcionalidade. Em SDKs .NET ou Java, você usa um cliente de processador de eventos (EventProcessorClient) e em SDKs Python e Java Script, você usa EventHubConsumerClient. Na versão antiga do SDK, era o host do processador de eventos (EventProcessorHost) que suportava esses recursos.

Para a maioria dos cenários de produção, recomendamos que você use o cliente processador de eventos para eventos de leitura e processamento. O cliente do processador tem o objetivo de fornecer uma experiência robusta para processar eventos em todas as partições de um hub de eventos de forma eficaz e tolerante a falhas, fornecendo um meio de desfazer seu progresso. Os clientes de processadores de eventos também são capazes de trabalhar cooperativamente dentro do contexto de um grupo de consumidores para um determinado hub de eventos. Os clientes gerenciarão automaticamente a distribuição e o balanceamento do trabalho à medida que as instâncias estiverem disponíveis ou indisponíveis para o grupo.

## <a name="partition-ownership-tracking"></a>Acompanhamento de propriedade da partição

Uma instância de processador de eventos normalmente possui e processa eventos a partir de uma ou mais partições. A propriedade de partições é distribuída uniformemente entre todas as instâncias ativas do processador de eventos associadas a um hub de eventos e a combinação de grupos de consumidores. 

Cada processador de eventos recebe um identificador exclusivo e reivindica a propriedade de partições adicionando ou atualizando uma entrada em uma loja de pontos de verificação. Todas as instâncias do processador de eventos se comunicam com esta loja periodicamente para atualizar seu próprio estado de processamento, bem como para aprender sobre outras instâncias ativas. Esses dados são então usados para equilibrar a carga entre os processadores ativos. Novas instâncias podem se juntar ao pool de processamento para aumentar a escala. Quando as instâncias são para baixo, seja devido a falhas ou para reduzir a escala, a propriedade da partição é graciosamente transferida para outros processadores ativos.

Os registros de propriedade de partição na loja de pontos de verificação acompanham o namespace do Event Hubs, o nome do hub de eventos, o grupo de consumidores, o identificador do processador de eventos (também conhecido como proprietário), o id de partição e o último tempo modificado.



| Namespace do Hubs de Eventos               | Nome do Hub de Eventos | **Grupo de consumidores** | Proprietário                                | Partition ID | Último tempo modificado  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Cada instância do processador de eventos adquire a propriedade de uma partição e começa a processar a partição do último [ponto de verificação](# Checkpointing)conhecido . Se um processador falhar (a VM é desligada), outras instâncias detectarão isso olhando para o último tempo modificado. Outras instâncias tentam obter a propriedade das partições anteriormente de propriedade da instância inativa, e o armazenamento de checkpoint garante que apenas uma das instâncias consegue reivindicar a propriedade de uma partição. Então, em qualquer momento, há no máximo um processador recebendo eventos de uma partição.

## <a name="receive-messages"></a>Receber mensagens

Quando você cria um processador de eventos, você especifica as funções que processarão eventos e erros. Cada chamada para a função que processa eventos fornece um único evento a partir de uma partição específica. É sua responsabilidade lidar com este evento. Se você quiser ter certeza de que o consumidor processa cada mensagem pelo menos uma vez, você precisa escrever seu próprio código com lógica de repetição. Mas tenha cuidado com mensagens suspeitas.

Recomendamos que você faça as coisas relativamente rápido. Ou seja, faça o menor processamento possível. Se você precisa escrever para armazenamento e fazer algum roteamento, é melhor usar dois grupos de consumidores e ter dois processadores de eventos.

## <a name="checkpointing"></a>Ponto de verificação

*O checkpoint* é um processo pelo qual um processador de eventos marca ou compromete a posição do último evento processado com sucesso dentro de uma partição. A marcação de um ponto de verificação é normalmente feita dentro da função que processa os eventos e ocorre em uma base por partição dentro de um grupo de consumidores. 

Se um processador de eventos se desconectar de uma partição, outra instância poderá retomar o processamento da partição no ponto de verificação que foi anteriormente cometido pelo último processador dessa partição nesse grupo de consumidores. Quando o processador se conecta, ele passa o deslocamento para o centro de eventos para especificar o local para iniciar a leitura. Desta forma, você pode usar o checkpoint para marcar eventos como "completos" por aplicativos a jusante e para fornecer resiliência quando um processador de eventos cai. É possível retornar aos dados mais antigos, especificando um deslocamento inferior desse processo de ponto de verificação. 

Quando o ponto de verificação é realizado para marcar um evento como processado, uma entrada na loja de pontos de verificação é adicionada ou atualizada com o número de deslocamento e seqüência do evento. Os usuários devem decidir a frequência de atualização do ponto de verificação. A atualização após cada evento processado com sucesso pode ter implicações de desempenho e custos, pois desencadeia uma operação de gravação na loja de pontos de verificação subjacente. Além disso, o checkpoint de cada evento é um indicativo de um padrão de mensagens enfileirados para o qual uma fila de Ônibus de Serviço pode ser uma opção melhor do que um hub de eventos. A ideia por trás dos Hubs de eventos é que você obtenha entregas "pelo menos uma vez" em grande escala. Tornando seus sistemas downstream idempotentes, é fácil de se recuperar de falhas ou reinicia que resultam nos mesmos eventos que está sendo recebidos várias vezes.

> [!NOTE]
> Se você estiver usando o Azure Blob Storage como o armazenamento de pontos de verificação em um ambiente que suporta uma versão diferente do Storage Blob SDK do que aqueles normalmente disponíveis no Azure, você precisará usar o código para alterar a versão aPI do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se você estiver executando [hubs de eventos em uma versão 2002 do Azure Stack Hub,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a versão mais alta disponível para o serviço de armazenamento é a versão 2017-11-09. Neste caso, você precisa usar o código para segmentar a versão de API do serviço de armazenamento para 2017-11-09. Para obter um exemplo sobre como segmentar uma versão específica da API de armazenamento, consulte essas amostras no GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) ou [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>Instâncias de processador e de segurança do thread

Por padrão, processador de eventos ou consumidor é seguro de rosca e se comporta de forma síncrona. Quando os eventos chegam para uma partição, a função que processa os eventos é chamada. Mensagens e chamadas subseqüentes para esta função fazem fila nos bastidores à medida que a bomba de mensagens continua a ser executada em segundo plano em outros segmentos. Esse thread-safe elimina a necessidade de coleções thread-safe e aumenta drasticamente o desempenho.

## <a name="next-steps"></a>Próximas etapas
Veja as seguintes partidas rápidas:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
