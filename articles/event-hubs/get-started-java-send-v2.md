---
title: Enviar ou receber eventos de Hubs de Eventos do Azure usando o Java (mais recente)
description: Este artigo fornece um passo a passo de como criar um aplicativo Java que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure-messaging-eventhubs mais recente.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: d9d22374868f3befd659918c532f339d49ba1642
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032044"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Usar o Java para enviar eventos ou receber eventos dos Hubs de Eventos do Azure (azure-messaging-eventhubs)
Este início rápido mostra como criar aplicativos Java que enviam ou recebem eventos de Hubs de Eventos do Azure. 

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

> [!IMPORTANT]
> Este início rápido usa o novo pacote **azure-messaging-eventhubs**. Para um início rápido que usa os pacotes antigos **azure-eventhubs** e **azure-eventhubs-eph**, confira [este artigo](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um ambiente de desenvolvimento Java. Este tutorial usa o [Eclipse](https://www.eclipse.org/). É necessário um JDK (Java Development Kit) com a versão 8 ou superior. 
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A chave de acesso será usada no código que você escreverá posteriormente no tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo Java para enviar eventos para um hub de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar a referência à biblioteca de Hubs de Eventos do Azure

A biblioteca de clientes Java para Hubs de Eventos está disponível para uso em projetos Maven no [Repositório Central do Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência em seu arquivo do projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `SimpleSend` e adicione o seguinte código à classe:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
    public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Cadeia de conexão e hub de eventos
Esse código usa a cadeia de conexão para o namespace de Hubs de Eventos e o nome do hub de eventos para criar um cliente de Hubs de Eventos. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Criar um cliente produtor de Hubs de Eventos 
Esse código cria um objeto de cliente produtor que é usado para produzir/enviar eventos para o hub de eventos. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducer();
```

### <a name="prepare-a-batch-of-events"></a>Preparar um lote de eventos
Esse código prepara um lote de eventos. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Enviar o lote de eventos para o hub de eventos
Esse código envia o lote de eventos que você preparou na etapa anterior para o hub de eventos. Os blocos de código a seguir na operação de envio. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Fechar e limpar
Esse código fecha o produtor. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Código completo para enviar eventos
Este é o código completo para enviar eventos para o hub de eventos. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducer();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Compile o programa e certifique-se de que não existem erros. Você executará esse programa depois de executar o programa receptor. 

## <a name="receive-events"></a>Receber eventos
O código neste tutorial se baseia no [exemplo de EventProcessorClient no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), que você pode examinar para ver todo o aplicativo funcional.

### <a name="create-a-java-project"></a>Criar um projeto Java

A biblioteca de cliente Java para os Hubs de Eventos está disponível para uso em projetos do Maven por meio do [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), e pode ser referenciada usando a seguinte declaração de dependência dentro do arquivo de projeto do Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Use o código a seguir para criar uma nova classe chamada `Receiver`. Substitua os espaços reservados pelos valores usados durante a criação do hub de eventos e da conta de armazenamento:
   
   ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;

    public class Receiver {

        private static final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static final String eventHubName = "EVENT HUB NAME";
    
        public static void main(String[] args) throws Exception {

            // function to process events
            Consumer<EventContext> processEvent = eventContext  -> {
                System.out.print("Received event: ");
                // print the body of the event
                System.out.println(eventContext.getEventData().getBodyAsString());
                eventContext.updateCheckpoint();
            };

            // function to process errors
            Consumer<ErrorContext> processError = errorContext -> {
                // print the error message
                System.out.println(errorContext.getThrowable().getMessage());
            };

            EventProcessorBuilder eventProcessorBuilder = new EventProcessorBuilder()
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .connectionString(connectionString, eventHubName)
                .processEvent(processEvent)
                .processError(processError)
                .checkpointStore(new InMemoryCheckpointStore());
        
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();
            System.out.println("Starting event processor");
            eventProcessorClient.start();

            System.out.println("Press enter to stop.");
            System.in.read();

            System.out.println("Stopping event processor");
            eventProcessor.stop();
            System.out.println("Event processor stopped.");
    
            System.out.println("Exiting process");
        }
    }
    ```
    
2. Baixe o arquivo **InMemoryCheckpointStore.java** do [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs) e adicione-o ao seu projeto. 
3. Compile o programa e certifique-se de que não existem erros. 

## <a name="run-the-applications"></a>Executar os aplicativos
1. Execute primeiro o aplicativo **receptor**.
1. Em seguida, execute o aplicativo **remetente**. 
1. Na janela do aplicativo **receptor**, confirme se você vê os eventos que foram publicados pelo aplicativo remetente.
1. Pressione **ENTER** na janela do aplicativo receptor para interromper o aplicativo. 

## <a name="next-steps"></a>Próximas etapas
Confira [exemplos de SDK do Java no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

