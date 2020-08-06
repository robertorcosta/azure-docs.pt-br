---
title: Como usar filas do azure/barramento de serviço no Node.js
description: Aprenda a escrever um programa Node.js para enviar mensagens para uma fila do Barramento de Serviço e receber mensagens dela usando o novo pacote @azure/service-bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: 63b8c33b61d6bff28eca98929e344df7ea54e779
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430671"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Início Rápido: Como usar filas do barramento de serviço com o Node.js e o pacote azure/service-bus
Neste tutorial, você aprende a escrever um programa Nodejs para enviar e receber mensagens de uma fila do Barramento de Serviço usando o novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Esse pacote usa o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [azure-sb](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do Barramento de Serviço](/rest/api/servicebus/service-bus-runtime-rest). As amostras são escritas em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a cadeia de conexão para sua instância do Barramento de Serviço e o nome da fila que você criou. Usaremos esses valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que você pode copiar e executar usando o [Node.js](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo Node.js, confira [Criar e implantar um aplicativo do Node.js em um site da Web do Azure](../app-service/app-service-web-get-started-nodejs.md) ou [Serviço de Nuvem do Node.js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) não dá suporte à criação de filas ainda. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se quiser criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
Para instalar o pacote npm para o Barramento de Serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
A interação com uma fila do Barramento de Serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient). Depois de ter o cliente de fila, você pode criar um remetente e usar o método [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole nele o código abaixo. Esse código enviará 10 mensagens para sua fila.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de enviar mensagens para uma fila do Barramento de Serviço.

As mensagens têm algumas propriedades padrão, como `label` e `messageId`, que você pode definir ao enviar. Se você quiser definir alguma propriedade personalizada, use o `userProperties`, que é um objeto json que pode conter pares de chave-valor de seus dados personalizados.

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). Não há nenhum limite no número de mensagens armazenadas em uma fila, mas há um limite no tamanho total das mensagens armazenadas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
A interação com uma fila do Barramento de Serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient). Depois que você tiver o cliente de fila, você pode criar um receptor e usar o método [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole nele o código abaixo. Esse código tentará receber 10 mensagens da sua fila. A contagem real que você recebe depende do número de mensagens na fila e da latência de rede.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de receber mensagens de uma fila do Barramento de Serviço.

O método [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) usa um `ReceiveMode`, que é um enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se você usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()` ou `deadletter()` na mensagem.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir.
- [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
- Fazer check-out de outros [exemplos do Nodejs para o Barramento de Serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de desenvolvedores do Node. js](https://azure.microsoft.com/develop/nodejs/)

