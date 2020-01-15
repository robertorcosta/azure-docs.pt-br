---
title: Enviar e receber eventos usando o Node.js – Hubs de Eventos do Azure
description: Este artigo apresenta instruções passo a passo para a criação de um aplicativo Node.js que envia eventos de Hubs de Eventos do Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: 9ea6febc781422a72ac6547338c8b21239331083
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942510"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Enviar ou receber eventos de Hubs de Eventos do Azure usando o Node.js 

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicativos Node.js para enviar ou receber eventos de um hub de eventos.

> [!IMPORTANT]
> Este guia de início rápido usa a versão 5 do SDK de script Java dos hubs de eventos do Azure. Para um início rápido que usa a versão 2 antiga do SDK do script Java, consulte [Este artigo](event-hubs-node-get-started-send.md). Se você estiver usando a versão 2 do SDK, recomendamos que migre seu código para a versão mais recente. Para obter detalhes, consulte o [Guia de migração](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial. Em seguida, obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.


### <a name="install-npm-packages"></a>Instalar pacotes npm
Para instalar o [pacote npm para Hubs de Eventos](https://www.npmjs.com/package/@azure/event-hubs), abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando

```shell
npm install @azure/event-hubs
```

Para o lado de recebimento, você precisa instalar mais dois pacotes. Neste guia de início rápido, você usará o armazenamento de BLOBs do Azure para persistir pontos de verificação para que o programa não leia os eventos já lidos. Ele verifica os metadados em mensagens recebidas em intervalos regulares em um blob. Com essa abordagem, fica fácil continuar a receber mensagens mais tarde exatamente no ponto em que você parou.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Esta seção mostra como criar um aplicativo Node.js que envia eventos a um hub de eventos. 

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `send.js` e cole o código a seguir nele. 

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {
    
      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);
    
      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    
    
      // send the batch to the event hub
      await producer.sendBatch(batch);
    
      // close the producer client
      await producer.close();
    
      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Não se esqueça de substituir a **cadeia de conexão** e os valores de nome do hub de **eventos** no código. 
5. Execute o comando `node send.js` para executar esse arquivo. Isso enviará um lote de três eventos para o Hub de eventos
6. No portal do Azure, você pode verificar se o Hub de eventos recebeu as mensagens. Alterne para a exibição **mensagens** na seção **métricas** . Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ele mostre que as mensagens foram recebidas. 

    [![verificar se o Hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código-fonte completo com comentários mais informativos, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Parabéns! Agora você enviou eventos a um hub de eventos. 


## <a name="receive-events"></a>Receber eventos 
Esta seção mostra como receber eventos de um hub de eventos usando um repositório de pontos de verificação de blob do Azure em um aplicativo node. js. Ele usa um ponto de verificação nos metadados das mensagens recebidas em intervalos regulares em um Azure Storage Blob. Com essa abordagem, fica fácil continuar a receber mensagens mais tarde exatamente no ponto em que você parou.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento do Azure e um contêiner de BLOBs
Siga estas etapas para criar uma conta de armazenamento do Azure em um contêiner de BLOBs. 

1. [Crie uma conta de Armazenamento do Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Crie um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obter a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anote a cadeia de conexão e o nome do contêiner. Você os usará no código de recebimento. 

### <a name="write-code-to-receive-events"></a>Escrever código para receber eventos

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `receive.js` e cole o código a seguir nele. Consulte os comentários de código para obter detalhes. 
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";
    
    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);
    
      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);
    
      // subscribe for the events and specify handlers for processing the events and errors. 
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint 
            await context.updateCheckpoint(events[events.length - 1]);
          },
    
          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );
        
      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Não se esqueça de especificar os **seguintes valores** no código: 
    - Cadeia de conexão para o namespace de hubs de eventos
    - Nome do hub de eventos
    - Cadeia de conexão para a conta de armazenamento do Azure
    - Nome do contêiner de BLOBs
5. Em seguida, execute o comando `node receive.js` em um prompt de comando para executar esse arquivo. Você deve ver as mensagens sobre eventos recebidos na janela. 

    > [!NOTE]
    > Para obter o código-fonte completo com comentários mais informativos, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Parabéns! Agora você recebeu eventos do hub de eventos. O programa receptor receberá eventos de todas as partições do grupo de consumidores padrão em seu hub de eventos

## <a name="next-steps"></a>Próximos passos
Confira estes exemplos no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Amostras do TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)