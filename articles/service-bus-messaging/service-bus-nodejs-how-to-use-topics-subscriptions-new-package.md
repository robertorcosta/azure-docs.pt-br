---
title: Usar tópicos e assinaturas do azure/service-bus com o Node.js
description: 'Início Rápido: Aprenda a usar assinaturas e tópicos do Barramento de Serviço no Azure por meio de um aplicativo Node.js.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: fafdf18a3593f7ec444e9970d99807cbfe0cb13d
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080619"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Início Rápido: Como usar tópicos e assinaturas do Barramento de Serviço do Azure com o Node.js e o pacote azure/service-bus
Neste tutorial, você aprenderá a escrever um programa Node.js para enviar mensagens para um tópico do Barramento de Serviço e receber mensagens de uma assinatura do Barramento de Serviço usando o novo pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Esse pacote usa o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [azure-sb](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do Barramento de Serviço](/rest/api/servicebus/service-bus-runtime-rest). As amostras são escritas em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver um tópico e uma assinatura para trabalhar, siga as etapas no artigo [Usar o portal do Azure para criar tópicos e assinaturas do Barramento de Serviço](service-bus-quickstart-topics-subscriptions-portal.md) para criá-los. Anote a cadeia de conexão para sua instância do Barramento de Serviço e os nomes do tópico e da assinatura que você criou. Usaremos esses valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que você pode copiar e executar usando o [Node.js](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo Node.js, confira [Criar e implantar um aplicativo do Node.js em um site da Web do Azure](../app-service/quickstart-nodejs.md) ou [Serviço de Nuvem do Node.js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) não dá suporte à criação de tópicos e assinaturas ainda. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se quiser criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
Para instalar o pacote npm para o Barramento de Serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta em que você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
A interação com um tópico do Barramento de Serviço começa com a instanciação da classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [TopicClient](/javascript/api/@azure/service-bus/topicclient). Depois de ter o cliente de tópico, você pode criar um remetente e usar o método [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole nele o código abaixo. Esse código enviará 10 mensagens para seu tópico.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome de seu tópico no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo. 

Parabéns! Você acabou de enviar mensagens para uma fila do Barramento de Serviço.

As mensagens têm algumas propriedades padrão, como `label` e `messageId`, que você pode definir ao enviar. Se você quiser definir alguma propriedade personalizada, use o `userProperties`, que é um objeto json que pode conter pares de chave-valor de seus dados personalizados.

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). Não há limite no número de mensagens mantidas em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB. Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
A interação com uma assinatura do Barramento de Serviço começa com a instanciação da classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [SubscriptionClient](/javascript/api/@azure/service-bus/subscriptionclient). Depois que você tiver o cliente de assinatura, você pode criar um receptor e usar o método [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole nele o código abaixo. Esse código tentará receber 10 mensagens da sua assinatura. A contagem real que você recebe depende do número de mensagens na assinatura e da latência de rede.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e os nomes de seu tópico e sua assinatura no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de receber mensagens de uma assinatura do Barramento de Serviço.

O método [createReceiver](/javascript/api/@azure/service-bus/subscriptionclient#createreceiver-receivemode-) usa um `ReceiveMode`, que é um enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se você usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()` ou `deadletter()` na mensagem.

## <a name="subscription-filters-and-actions"></a>Filtros e ações de assinatura
O Barramento de Serviço é compatível com [filtros e ações em assinaturas](topic-filters.md), que permite filtrar as mensagens de entrada para uma assinatura e editar suas propriedades.

Depois de ter uma instância de um `SubscriptionClient`, você pode usar os métodos abaixo para obter, adicionar e remover regras na assinatura para controlar os filtros e as ações.

- getRules
- addRule
- removeRule

Cada assinatura tem uma regra padrão que usa o filtro true para permitir todas as mensagens de entrada. Ao adicionar uma nova regra, remova o filtro padrão para que o filtro em sua nova regra funcione. Se uma assinatura não tiver regras, ela não receberá mensagens.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir.

- [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
- Fazer check-out de outros [exemplos do Nodejs para o Barramento de Serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de desenvolvedores do Node. js](https://azure.microsoft.com/develop/nodejs/)