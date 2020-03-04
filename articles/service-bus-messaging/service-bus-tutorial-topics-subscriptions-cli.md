---
title: 'Tutorial: Atualizar sortimento de estoque de varejo usando canais de publicação/assinatura e filtros de tópicos com a CLI do Azure'
description: 'Tutorial: Neste tutorial, você aprenderá como enviar e receber mensagens de um tópico e uma assinatura e como adicionar e usar regras de filtro usando a CLI do Azure'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 0bd0d8eb8abe6f320f73e35b1e3b08e1d8dc1de3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718915"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Tutorial: Atualizar o estoque usando a CLI e tópicos/assinaturas

O Barramento de Serviço do Microsoft Azure é um serviço de mensagens na nuvem multilocatário que envia informações entre aplicativos e serviços. Operações assíncronas oferecem um sistema de mensagens agenciado e flexível, juntamente com recursos de mensagens PEPS (primeiro a entrar, primeiro a sair) e de publicação/assinatura. Este tutorial mostra como usar tópicos e assinaturas do Barramento de Serviço em um cenário de estoque de varejo com canais de publicação/assinatura usando a CLI do Azure e Java.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando a CLI do Azure
> * Adicionar filtros de tópicos usando a CLI do Azure
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Um exemplo desse cenário é uma atualização de sortimento de estoque para várias lojas de varejo. Nesse cenário, cada loja ou conjunto de lojas recebe mensagens que são destinadas para atualizar os respectivos sortimentos. Este tutorial mostra como implementar esse cenário usando assinaturas e filtros. Primeiro, você cria um tópico com 3 assinaturas, adiciona algumas regras e filtros e envia e recebe mensagens do tópico e das assinaturas.

![topic](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para desenvolver um aplicativo de Barramento de Serviço com Java, você deve ter o seguinte instalado:

- [Java Development Kit](https://aka.ms/azure-jdks), última versão.
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos e assinaturas do Barramento de Serviço

Cada [assinatura a um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos tem total compatibilidade de protocolo e semântica com filas do Barramento de Serviço. Os tópicos do Barramento de Serviço dão suporte a uma ampla matriz de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam as propriedades da mensagem. Cada vez que uma regra corresponde, é produzida uma mensagem. Para saber mais sobre regras, filtros e ações, consulte este [link](topic-filters.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Assim que a CLI for instalada, abra um prompt de comando e envie os comandos a seguir para entrar no Azure. Essas etapas não são necessárias se você estiver usando o Cloud Shell:

1. Se você estiver usando a CLI do Azure no local, execute o comando a seguir para entrar no Azure. Essa etapa de logon não será necessária se você estiver executando esses comandos no Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Defina o contexto da assinatura atual para a assinatura do Azure que você deseja usar:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Usar a CLI para provisionar recursos

Emita os comandos a seguir para provisionar os recursos do Barramento de Serviço. Verifique se todos os espaços reservados foram substituídos pelos valores apropriados:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Depois que o último comando executar, copie e cole a cadeia de conexão e o nome da fila selecionados em um local temporário, como no Bloco de Notas. Isso será necessário na próxima etapa.

## <a name="create-filter-rules-on-subscriptions"></a>Criar regras de filtro em assinaturas

Depois que o namespace e tópicos/assinaturas forem provisionados e você tiver as credenciais necessárias, você está pronto para criar regras de filtro nas assinaturas e depois enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

1. Verifique se o Cloud Shell está aberto e exibindo o prompt de Bash.

2. Clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navegue até a pasta de exemplo `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Observe que, no shell do Bash, os comandos diferenciam maiúsculas de minúsculas, e os separadores de caminho devem ser barras invertidas.

3. Emita o comando a seguir para compilar o aplicativo:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Para executar o programa, execute o comando a seguir. Verifique se os espaços reservados foram substituídos pela cadeia de conexão e pelo nome do tópico obtidos na etapa anterior:

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Observe 10 mensagens sendo enviadas para o tópico e, subsequentemente, recebidas pelas assinaturas individuais:

   ![saída do programa](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Limpar recursos

Execute o comando a seguir para remover o grupo de recursos, o namespace e todos os recursos relacionados:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz.

### <a name="get-connection-string-and-queue"></a>Obter cadeia de conexão e fila

Primeiro, o código declara um conjunto de variáveis, que conduz a execução restante do programa:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

A cadeia de conexão e o nome do tópico são os únicos valores adicionados por meio de parâmetros de linha de comando e passados para `main()`. A execução de código atual é acionada no método `run()` e envia e depois recebe mensagens do tópico:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Criar o cliente de tópico para enviar mensagens

Para enviar e receber mensagens, o método `sendMessagesToTopic()` cria uma instância de cliente de tópico, que usa a cadeia de conexão e o nome do tópico e depois chama outro método que envia as mensagens:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Receber mensagens a partir das assinaturas individuais

O método `receiveAllMessages()` chama o método `receiveAllMessageFromSubscription()`, o qual, em seguida, cria um cliente de assinatura por chamada e recebe mensagens de assinaturas individuais:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você provisionou recursos usando a CLI do Azure e, em seguida, enviou e recebeu mensagens de um tópico do Barramento de Serviço e suas respectivas assinaturas. Você aprendeu como:

> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Para obter mais exemplos de envio e recebimento de mensagens, comece com os [Exemplos de Barramento de Serviço no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre como usar os recursos de publicação/assinatura do Barramento de Serviço.

> [!div class="nextstepaction"]
> [Atualizar estoque usando o PowerShell e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
