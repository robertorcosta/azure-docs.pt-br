---
title: Enviar ou receber eventos de Hubs de Eventos do Azure usando o Node.js (mais recente)
description: Este artigo fornece um passo a passo para criar um aplicativo Node.js que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure/event-hubs versão 5 mais recente.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906378"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Enviar eventos para Hubs de Eventos ou receber eventos deles usando o Node.js (azure/event-hubs versão 5)

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um hub de eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter mais informações, confira [Visão geral dos Hubs de Eventos](event-hubs-about.md) e [Recursos dos Hubs de Eventos](event-hubs-features.md).

Este início rápido descreve como criar aplicativos Node.js que podem enviar eventos para um hub de eventos ou receber dele.

> [!IMPORTANT]
> Este início rápido usa a versão 5 do SDK do JavaScript dos Hubs de Eventos do Azure. Para obter um início rápido que usa a versão 2 do SDK do JavaScript, confira [este artigo](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), crie uma antes de começar.  
- Node.js versão 8.x ou posterior. Baixe a [versão LTS (suporte de longo prazo)](https://nodejs.org) mais recente.  
- Visual Studio Code (recomendado) ou qualquer outro IDE (ambiente de desenvolvimento integrado).  
- Um namespace dos Hubs de Eventos e um hub de eventos ativos. Para criá-los, execute as seguintes etapas: 

   1. No [portal do Azure](https://portal.azure.com), crie um namespace do tipo *Hubs de Eventos* e obtenha as credenciais de gerenciamento de que seu aplicativo precisa para se comunicar com o hub de eventos. 
   1. Para criar o namespace e o hub de eventos, siga as instruções em [Início Rápido: Criar um hub de eventos usando o portal do Azure](event-hubs-create.md).
   1. Continue seguindo as instruções neste início rápido. 
   1. Para obter a cadeia de conexão para seu namespace do Hub de Eventos, siga as instruções em [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Registre a cadeia de conexão para usar posteriormente neste início rápido.

### <a name="install-the-npm-package"></a>instalar o pacote npm
Para instalar o [pacote npm (Gerenciador de Pacotes do Nó) para Hubs de Eventos](https://www.npmjs.com/package/@azure/event-hubs), abra um prompt de comando que tenha *npm* no caminho dele, altere o diretório para a pasta em que você deseja manter seus exemplos e, em seguida, execute este comando:

```shell
npm install @azure/event-hubs
```

Para o lado receptor, você precisará instalar mais dois pacotes. Neste início rápido, você usará o Armazenamento de Blobs do Azure para persistir pontos de verificação para que o programa não leia os eventos que ele já leu. Ele executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares em um blob. Essa abordagem facilita continuar a receber mensagens mais tarde exatamente do ponto em que você parou.

Execute os seguintes comandos:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Nesta seção, você criará um aplicativo Node.js que envia eventos a um hub de eventos.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com).
1. Crie um arquivo chamado *send.js* e cole o seguinte código nele:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. No código, use valores reais para substituir o seguinte:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Execute `node send.js` para realizar este arquivo. Esse comando envia um lote de três eventos para seu hub de eventos.
1. No portal do Azure, verifique se o hub de eventos recebeu as mensagens. Na seção **Métricas**, alterne para a exibição **Mensagens**. Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ela mostre que as mensagens foram recebidas.

    [![Verificar se o hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código-fonte completo, incluindo comentários informativos adicionais, acesse a [página sendEvents do GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Parabéns! Agora você enviou eventos a um hub de eventos.


## <a name="receive-events"></a>Receber eventos
Nesta seção, você receberá eventos de um hub de eventos usando um repositório de pontos de verificação do Armazenamento de Blobs do Azure em um aplicativo Node.js. Ele executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares em um Azure Storage Blob. Essa abordagem facilita continuar a receber mensagens mais tarde exatamente do ponto em que você parou.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Criar uma conta de armazenamento e um contêiner de blobs do Azure
Para criar uma conta de armazenamento do Azure e um contêiner de blobs nela, execute as seguintes ações:

1. [Crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Crie um contêiner de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Registre a cadeia de conexão e o nome do contêiner para usá-los posteriormente no código de recebimento.

### <a name="write-code-to-receive-events"></a>Gravar código para receber eventos

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com).
1. Crie um arquivo chamado *receive.js* e cole o seguinte código nele:

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. No código, use valores reais para substituir os seguintes valores:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Execute `node receive.js` em um prompt de comando para executar esse arquivo. A janela deve exibir mensagens sobre os eventos recebidos.

    > [!NOTE]
    > Para ver o código-fonte completo, incluindo comentários informativos adicionais, acesse a [página receiveEventsUsingCheckpointStore.js do GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Parabéns! Agora você recebeu eventos do seu hub de eventos. O programa receptor receberá eventos de todas as partições do grupo de consumidores padrão no hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Confira estes exemplos no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Exemplos de TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
