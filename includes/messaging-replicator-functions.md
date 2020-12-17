---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc6b7553d240de05404d24f828a5f7db14772f93
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657332"
---
## <a name="what-is-a-replication-task"></a>O que é uma tarefa de replicação?

Uma tarefa de Replicação recebe eventos de uma origem e os encaminha para um destino.
A maioria das tarefas de replicação encaminhará eventos inalterados e, no máximo, realizará mapeamento entre estruturas de metadados se os protocolos de origem e de destino forem diferentes. 

As tarefas de replicação geralmente são sem estado, o que significa que elas não compartilham o estado ou outros efeitos colaterais em execuções sequenciais ou paralelas de uma tarefa. Isso também é verdadeiro para envio em lote e encadeamento, que pode ser implementado na parte superior do estado existente de um fluxo. 

Isso torna as tarefas de replicação diferentes das tarefas de agregação, que geralmente são monitoradas, e são o domínio de estruturas e serviços de análise, como [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Aplicativos e tarefas de replicação no Azure Functions

No Azure Functions, uma tarefa de replicação é implementada usando um [gatilho](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md) que adquire uma ou mais mensagens de entrada de uma fonte configurada e uma [Associação de saída](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md#binding-direction) que encaminha as mensagens copiadas da origem para um destino configurado. 

| Gatilho  | Saída |
|----------|--------|
| [Gatilho de hubs de eventos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Associação de saída dos hubs de eventos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Gatilho do barramento de serviço do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Associação de saída do barramento de serviço do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Gatilho do Hub IoT do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Associação de saída do Hub IoT do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Gatilho de grade de eventos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Associação de saída da grade de eventos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Gatilho de Armazenamento de Filas do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Associação de saída do armazenamento de filas do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Gatilho de Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka Associação de saída](https://github.com/azure/azure-functions-kafka-extension)
| [Gatilho RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Associação de saída do RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Associação de saída dos hubs de notificação do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Associação de saída do serviço de Signaler do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Associação de saída twilio SendGrid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

As tarefas de replicação são implantadas como no aplicativo de replicação por meio dos mesmos métodos de implantação que qualquer outro aplicativo Azure Functions. Você pode configurar várias tarefas no mesmo aplicativo. 

Com o Azure Functions Premium, vários aplicativos de replicação podem compartilhar o mesmo pool de recursos subjacente, chamado de plano do serviço de aplicativo. Isso significa que você pode posicionar facilmente as tarefas de replicação escritas no .NET com tarefas de replicação escritas em Java, por exemplo. Isso será importante se você quiser tirar proveito de bibliotecas específicas, como o Apache Camel, que estão disponíveis apenas para Java e se são a melhor opção para um determinado caminho de integração, embora normalmente você prefira uma linguagem e um tempo de execução diferentes para outras tarefas de replicação. 

Sempre que disponível, você deve preferir os gatilhos orientados a lote sobre gatilhos que fornecem eventos ou mensagens individuais e sempre deve obter o evento completo ou a estrutura da mensagem em vez de confiar nas [expressões de associação de parâmetro](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)da função do Azure.

O nome da função deve refletir o par de origem e destino que você está conectando, e você deve prefixar referências a cadeias de conexão ou outros elementos de configuração nos arquivos de configuração do aplicativo com esse nome. 

### <a name="data-and-metadata-mapping"></a>Mapeamento de dados e metadados

Depois de decidir sobre um par de gatilho de entrada e Associação de saída, você precisará executar algum mapeamento entre os diferentes tipos de evento ou de mensagem, a menos que o tipo de gatilho e a saída sejam os mesmos.

Para tarefas de replicação simples que copiam mensagens entre hubs de eventos e barramento de serviço, você não precisa escrever seu próprio código, mas pode [proficar em uma biblioteca de utilitário que é fornecida](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) com os exemplos de replicação.

### <a name="retry-policy"></a>Política de Repetição

Para evitar a perda de dados durante o evento de disponibilidade em qualquer um dos lados de uma função de replicação, você precisa configurar a política de repetição para ser robusta. Consulte a [documentação do Azure Functions em repetições](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md) para configurar a política de repetição. 

As configurações de política escolhidas para os projetos de exemplo no [repositório de exemplo](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) configuram uma estratégia de retirada exponencial com intervalos de repetição de 5 segundos a 15 minutos com tentativas infinitas para evitar a perda de dados. 

Para o barramento de serviço, examine a seção ["usando o suporte de repetição na parte superior da resiliência do gatilho"](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender a interação de gatilhos e a contagem máxima de entrega definida para a fila.

### <a name="setting-up-a-replication-application-host"></a>Configurando um host de aplicativo de replicação

Um aplicativo de replicação é um host de execução para uma ou mais tarefas de replicação. 

É um aplicativo Azure Functions configurado para ser executado no plano de consumo ou (recomendado) em um plano Azure Functions Premium. Todos os aplicativos de replicação devem ser executados sob uma [identidade gerenciada atribuída pelo usuário ou pelo sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity.md). 

Os modelos do ARM (Azure Resource Manager vinculados) criam e configuram um aplicativo de replicação com:

* uma conta de armazenamento do Azure para acompanhar o progresso e os logs da replicação
* uma identidade gerenciada atribuída pelo sistema e 
* Monitoramento do Azure e integração de Application Insights para monitoramento.

Os aplicativos de replicação que devem acessar os hubs de eventos associados a uma VNet (rede virtual) do Azure devem usar o plano Azure Functions Premium e ser configurados para serem anexados à mesma VNet, que também é uma das opções disponíveis.


|       | Implantar | Visualizar  
|-------|------------------|--------------|---------------|
| **Plano de consumo do Azure Functions** | [![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions plano Premium** |[![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Azure Functions plano Premium com VNet** | [![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Exemplos

O [repositório de exemplos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) contém vários exemplos de tarefas de replicação que copiam eventos entre hubs de eventos e/ou entre entidades do barramento de serviço.

Para copiar o evento entre os hubs de eventos, use um gatilho de Hub de eventos com uma associação de saída do hub de eventos:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Para copiar mensagens entre entidades do barramento de serviço, use o gatilho do barramento de serviço e a associação de saída:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Os métodos auxiliares podem tornar mais fácil replicar entre os hubs de eventos e o barramento de serviço:

| Fonte      | Destino      | Entry Point 
|-------------|-------------|------------------------------------------------------------------------
| Hub de evento   | Hub de evento   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub de evento   | Barramento de Serviço | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Barramento de Serviço | Hub de evento   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Barramento de Serviço | Barramento de Serviço | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Monitoramento

Para saber como você pode monitorar seu aplicativo de replicação, consulte a [seção monitoramento](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) da documentação do Azure functions.

Uma ferramenta Visual especialmente útil para monitorar tarefas de replicação é o [mapa do aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/app-map)Application insights, que é gerado automaticamente a partir das informações de monitoramento capturadas e permite explorar a confiabilidade e o desempenho da origem da tarefa de replicação e das transferências de destino.

Para obter informações de diagnóstico imediatos, você pode trabalhar com a ferramenta do portal de [métricas em tempo real](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) , que fornece uma visualização de baixa latência dos detalhes do log.

## <a name="next-steps"></a>Próximas etapas

* [Implantações de Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies.md)
* [Diagnóstico de Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-diagnostics.md)
* [Opções de rede Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options.md)
* [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview.md)