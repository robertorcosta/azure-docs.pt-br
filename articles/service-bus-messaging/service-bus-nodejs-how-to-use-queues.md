---
title: Usar filas do barramento de serviço do Azure no node. js
description: Aprenda a usar as filas do Barramento de Serviço no Azure a partir de um aplicativo Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1aba29f8ed7cacb8f2911ae2d37358869e6a7730
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001130"
---
# <a name="use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Usar filas do barramento de serviço no Azure com node. js e o pacote Azure-SB
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, você aprenderá a criar aplicativos node. js para enviar mensagens e receber mensagens de uma fila do barramento de serviço do Azure usando o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) . Os exemplos são escritos em JavaScript e usam o [módulo do Azure](https://www.npmjs.com/package/azure) node. js que usa internamente o pacote Azure-SB.

O pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) usa [APIs REST de tempo de execução do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) que usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md)mais rápido. Para saber mais sobre o novo pacote, consulte [como usar filas do barramento de serviço com o pacote node. js e @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), caso contrário, continue lendo para ver como usar o pacote [do Azure](https://www.npmjs.com/package/azure) .

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando o Node. js neste tutorial. 
 

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
Criar um aplicativo Node.js em branco. Para obter instruções sobre como criar um aplicativo Node.js, veja a seção [Criar e implantar um aplicativo Node.js em um site do Azure][Create and deploy a Node.js application to an Azure Website] ou [Serviço de Nuvem do Node.js][Node.js Cloud Service] usando o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar o Barramento de Serviço do Azure, baixe e use o pacote do Azure Node.js. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote
1. Use a janela de comando **Windows PowerShell para Node.js** para navegar até a pasta **c:\\node\\sbqueues\\WebRole1** na qual você criou o aplicativo de exemplo.
2. Digite **NPM install Azure** na janela de comando, que deve resultar em uma saída semelhante ao exemplo a seguir:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node_modules** foi criada. Dentro dessa pasta, localize o pacote **do Azure** , que contém as bibliotecas necessárias para acessar as filas do barramento de serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao início do arquivo **server.js** do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma conexão do barramento de serviço do Azure
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter as informações necessárias para se conectar ao Barramento de Serviço. Se essa variável de ambiente não estiver definida, você deverá especificar as informações da conta ao chamar `createServiceBusService`.

Para obter um exemplo de como definir as variáveis de ambiente no [portal do Azure][Azure portal] para um site do Azure, consulte [aplicativo Web do node. js com armazenamento][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusService** permite que você trabalhe com filas de barramento de serviço. O código a seguir cria um objeto **ServiceBusService**. Adicione-o próximo ao início do arquivo **server.js** , após a instrução de importação do módulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao chamar `createQueueIfNotExists` no objeto **ServiceBusService**, a fila especificada (se houver) é retornada ou uma nova fila com o nome especificado é criada. O código a seguir usa `createQueueIfNotExists` para criar ou conectar-se à fila denominada `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O método `createServiceBusService` também dá suporte para opções adicionais, que permitem a substituição de configurações padrão da fila, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e a vida útil (TTL) como um minuto:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtros
É possível aplicar operações de filtragem opcionais às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar `next`, passando um retorno de chamada com a assinatura a seguir:

```javascript
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e depois de processar o `returnObject` (a resposta da solicitação para o servidor), o retorno de chamada deve invocar `next` se existir para continuar processando outros filtros ou invocar `finalCallback`, o que encerra a invocação de serviço.

Dois filtros que implementam a lógica de repetição são incluídos com o Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O código a seguir cria um objeto `ServiceBusService` que usa o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Para enviar uma mensagem para uma fila do Barramento de Serviço, seu aplicativo chamará o método `sendQueueMessage` no objeto **ServiceBusService**. Mensagens enviadas para (e recebidas de) as filas do Barramento de Serviço são objetos **BrokeredMessage** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando uma cadeia de caracteres como a mensagem. As propriedades padrão necessárias são preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada `myqueue` usando `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens mantidas em uma fila, mas há uma limitação no tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `receiveQueueMessage` no objeto **ServiceBusService**. Por padrão, as mensagens são excluídas da fila à medida que são lidas; no entanto, você pode ler (pico) e bloquear a mensagem sem excluí-la da fila configurando o parâmetro opcional `isPeekLock` como **true**.

O comportamento padrão de leitura e exclusão da mensagem como parte da operação Receive é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando ocorrer uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Como o barramento de serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `isPeekLock` for definido como **true**, o Receive se tornará uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método `deleteMessage` e fornecendo a mensagem a ser excluída como um parâmetro. O método `deleteMessage` marcará a mensagem como sendo consumida e a removerá da assinatura.

O exemplo a seguir demonstra como receber e processar mensagens usando `receiveQueueMessage`. Primeiro, o exemplo recebe e exclui uma mensagem, em seguida recebe a mensagem usando `isPeekLock` definido como **true** e, então, exclui a mensagem usando `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método `unlockMessage` no objeto **ServiceBusService**. Isso fará com que o barramento de serviço desbloqueie a mensagem dentro da fila e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `deleteMessage` seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Essa abordagem é geralmente chamada *de processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica adicional ao aplicativo para lidar com a entrega de mensagens duplicadas. Geralmente, ele é obtido usando a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas de entrega.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre filas, veja os seguintes recursos.

* [Filas, tópicos e assinaturas][Queues, topics, and subscriptions]
* Repositório do [SDK do Azure para Node][Azure SDK for Node] no GitHub
* [Centro de desenvolvedores do Node. js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
