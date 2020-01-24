---
title: Diagnóstico e rastreamento end-to-end de Barramento de Serviço do Azure | Microsoft Docs
description: Visão geral do diagnóstico e o rastreamento end-to-end de cliente do Barramento de Serviço
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: fa71ca7ea976ab4d724a061d0d0809cdb5767f4f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705744"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rastreamento distribuído e correlação por meio de mensagens do Barramento de Serviço

Um dos problemas comuns no desenvolvimento de microservices é a capacidade de operação de rastreamento de um cliente por meio de todos os serviços que estão envolvidos no processamento. É útil para depuração, análise de desempenho, testes A/B e outros cenários típicos de diagnóstico.
Uma parte desse problema é monitorar partes lógicas do trabalho. Inclui o resultado e latência de processamento de mensagem e chamadas de dependência externa. Outra parte é correlação desses eventos de diagnóstico além dos limites do processo.

Quando um produtor envia uma mensagem por meio de uma fila, normalmente acontece no escopo de alguma outra operação lógica, iniciado por algum outro cliente ou serviço. A mesma operação dará continuidade ao consumidor quando ele recebe uma mensagem. O produtor e o consumidor (e outros serviços que processam a operação), presumivelmente emitem eventos de telemetria para rastrear o fluxo e o resultado da operação. Para correlacionar esses eventos e rastrear a operação end-to-end, cada serviço que informa a telemetria deve gravar todos os eventos com um contexto de rastreamento.

Mensagens do Barramento de Serviço do Microsoft Azure definiu propriedades de carga que produtores e consumidores devem usar para passar esse contexto de rastreamento.
O protocolo se baseia o [protocolo HTTP Correlation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nome da propriedade        | Description                                                 |
|----------------------|-------------------------------------------------------------|
|  ID de diagnóstico       | Identificador exclusivo de uma chamada externa do produtor para a fila. Consulte [Request-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |
|  Correlation-Context | Contexto de operação, é propagado por todos os serviços envolvidos no processamento da operação. Para obter mais informações, consulte [contexto de correlação no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Rastreamento automático de cliente de .NET do Barramento de Serviço

Começando com a versão 3.0.0 o [Cliente de Barramento de Serviço do Microsoft Azure para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornece pontos de instrumentação de rastreamento que podem ser conectados por sistemas de rastreamento ou parte do código de cliente.
A instrumentação permite controlar todas as chamadas para o serviço de mensagens do Barramento de Serviço do lado do cliente. Se o processamento de mensagens é feito com o [padrão de manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), o processamento de mensagem também é instrumentado

### <a name="tracking-with-azure-application-insights"></a>Rastreando o Application Insights do Azure

O [Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece recursos de monitoramento de desempenho avançado, incluindo solicitações automáticas e rastreamento de dependências.

Dependendo do tipo do seu projeto, instale o SDK do Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instalar a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instalar a versão 2.2.0-beta2 ou superior.
Esses links fornecem detalhes sobre como instalar o SDK, criando recursos e configurando o SDK (se necessário). Para aplicativos diferentes do ASP.NET, consulte o artigo [Application Insights do Azure para aplicativos de Console](../azure-monitor/app/console.md).

Se você usar o [padrão do manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, você está pronto: todas as chamadas de Barramento de Serviço feitas pelo serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o exemplo a seguir para o controle de processamento de mensagem manual.

#### <a name="trace-message-processing"></a>Processamento de mensagem de rastreamento

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

Neste exemplo, `RequestTelemetry` é relatado para cada mensagem processada, com um carimbo de hora, a duração e o resultado (êxito). A telemetria também tem um conjunto de propriedades de correlação.
Rastreamentos aninhados e exceções relatadas durante o processamento da mensagem também são marcadas com propriedades de correlação que os representam como filhos do `RequestTelemetry`.

Caso faça chamadas para componentes externos com suporte durante o processamento da mensagem, eles também serão automaticamente controlados e correlacionados. Consulte [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../azure-monitor/app/custom-operations-tracking.md) para rastreamento manual e correlação.

### <a name="tracking-without-tracing-system"></a>Controle sem o sistema de rastreamento
Caso seu sistema de rastreamento não ofereça suporte ao controle de chamadas do Barramento de Serviço automático, você poderá considerar adicionar esse suporte em um sistema de rastreamento ou em seu aplicativo. Esta seção descreve eventos de diagnóstico enviados pelo cliente .NET do Barramento de Serviço.  

Cliente de .NET do Barramento de Serviço é instrumentado usando primitivos de rastreamento .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de rastreamento enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver nenhum ouvinte para os eventos DiagnosticSource, a instrumentação é desativada, mantendo o custo zero de instrumentação. O DiagnosticSource fornece todo o controle para o ouvinte:
- o ouvinte controla quais fontes e eventos escutar
- o ouvinte controla a amostragem e taxa do evento
- os eventos são enviados com uma carga que fornece o contexto completo para que você possa acessar e modificar o objeto de mensagem durante o evento

Familiarize-se com [Guia do Usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de continuar com a implementação.

Vamos criar um ouvinte de eventos do Barramento de Serviço no aplicativo do ASP.NET Core que grava logs com o Microsoft.Extension.Logger.
Ele usa a biblioteca [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) para assinar DiagnosticSource (também é fácil assinar DiagnosticSource sem ela)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Neste exemplo, o ouvinte registra duração, resultado, identificador exclusivo e a hora de início para cada operação de Barramento de Serviço.

#### <a name="events"></a>Eventos

Para cada operação, dois eventos são enviados: 'Start' e 'Stop'. Provavelmente, você só está interessado em eventos de 'Stop'. Eles fornecem o resultado da operação, bem como hora de início e duração como propriedades de uma atividade.

A carga do evento fornece um ouvinte com o contexto da operação, ele replica os parâmetros de entrada de API e valor de retorno. A carga do evento de 'Stop' tem todas as propriedades da carga do evento de 'Start', portanto você pode ignorar o evento de 'Start' completamente.

Todos os eventos também têm propriedades 'Entity' e 'Endpoint', eles são omitidos na tabela abaixo
  * `string Entity` - - Nome da entidade (fila, tópico, etc.)
  * `Uri Endpoint` - URL do endpoint do Barramento de Serviço

Cada evento de 'Stop' tem `Status` propriedade com `TaskStatus` operação assíncrona foi concluída, o que também é omitido na tabela a seguir para simplificar.

Aqui está a lista completa de operações disponíveis:

| Nome de operação | API controlada | Propriedades específicas de carga|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lista de mensagens sendo enviadas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Mensagem sendo processada<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Deslocamento de mensagem agendado<br/>`long SequenceNumber` -Número de sequência da mensagem agendada (carga do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Número de sequência da mensagem a ser cancelada | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - O número máximo de mensagens que podem ser recebidas.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - O ponto de partida a partir do qual procurar um lote de mensagens.<br/>`int RequestedMessageCount` - O número de mensagens a recuperar.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - A lista que contém os números de sequência a serem recebidos.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - A lista que contém os tokens de bloqueio das mensagens correspondentes para concluir.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - O token de bloqueio da mensagem correspondente é abandonado. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - O token de bloqueio da mensagem correspondente para adiar. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - O símbolo de bloqueio da mensagem correspondente a letra morta. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - O token de bloqueio da mensagem correspondente para renovar o bloqueio.<br/>`DateTime LockedUntilUtc` - Nova data e hora de expiração do token de bloqueio no formato UTC. (carga do evento de 'Stop')|
| Microsoft.Azure.ServiceBus.Process | Função lambda manipulador de mensagens fornecida no [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` - Mensagem sendo processada. |
| Microsoft.Azure.ServiceBus.ProcessSession | Função de lambda de manipulador de sessão de mensagem fornecida no [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` - Mensagem sendo processada.<br/>`IMessageSession Session` -Sessão que está sendo processado |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - A descrição da regra que fornece a regra para adicionar. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - Nome da regra a ser removida. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - Todas as regras associadas à assinatura. (Somente a carga 'Stop') |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - O sessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - O sessionId presente nas mensagens.<br/>`byte [] State` - Estado da sessão (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - O sessionId presente nas mensagens.<br/>`byte [] State` -Estado de sessão |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - O sessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.Exception | qualquer API instrumentado| `Exception Exception` -Instância de exceção |

Em todos os eventos, você pode acessar `Activity.Current` que contém o contexto da operação atual.

#### <a name="logging-additional-properties"></a>Propriedades adicionais de log

`Activity.Current` fornece contexto detalhado da operação atual e seus pais. Para obter mais informações, consulte a [Documentação da atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para mais detalhes.
Instrumentação de Barramento de Serviço fornece informações adicionais de `Activity.Current.Tags` - elas mantêm `MessageId` e `SessionId` sempre que estiverem disponíveis.

Atividades que acompanham evento 'Receber', 'Inspeção' e 'ReceiveDeferred' também pode ter `RelatedTo` marca. Ele contém uma lista distinta de `Diagnostic-Id`(s) de mensagens que foram recebidas como resultado.
Essa operação pode resultar em várias mensagens relacionadas a ser recebida. Além disso, o `Diagnostic-Id` não é conhecido quando a operação é iniciada, portanto as operações 'Receive' podem estar correlacionadas a operações de 'Processo' usando somente essa marca. É útil ao analisar problemas de desempenho para verificar quanto tempo demorou para receber a mensagem.

É uma maneira eficiente para as marcas de log iterar sobre eles, portanto adicionando marcas à forma do exemplo anterior 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável para fazer logon apenas uma parte dos eventos para reduzir o consumo de armazenamento ou sobrecarga de desempenho. Você pode fazer somente eventos de 'Stop' (como no exemplo anterior) ou a porcentagem de exemplo dos eventos. 
`DiagnosticSource` fornecem a maneira de conseguir isso com `IsEnabled` predicado. Para obter mais informações, consulte [Filtragem com base em contexto em DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto de desempenho.

`IsEnabled` é chamado na sequência a seguir:

1. `IsEnabled(<OperationName>, string entity, null)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observe que não há nenhum 'Start' ou 'Stop' no final. Usá-lo para filtrar as operações específicas ou filas. Se o retorno de chamada retorna `false`, os eventos para a operação não são enviados

   * Para as operações de 'Processo' e 'ProcessSession', você também receberá `IsEnabled(<OperationName>, string entity, Activity activity)` retorno de chamada. Use-o para filtrar eventos com base em `activity.Id` ou propriedades de marcas.
  
2. `IsEnabled(<OperationName>.Start)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Verifica se o evento 'Start' deva ser acionado. O resultado afeta apenas o evento 'Start', mas mais instrumentação não depende dele.

Não há nenhum `IsEnabled` para o evento 'Stop'.

Se algum resultado da operação for a exceção, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` é chamado. Você só pode assinar eventos de 'Exceções' e impedir o restante da instrumentação. Nesse caso, você ainda precisa tratar essas exceções. Desde que outros instrumentação é desabilitada, você não deve esperar contexto de rastreamento para fluir com as mensagens de consumidor para produtor.

Você pode usar `IsEnabled` também implementar estratégias de amostragem. Amostragem com base no `Activity.Id` ou `Activity.RootId` garante a amostragem consistente em todas as camadas (desde que ela é propagada pelo sistema de rastreamento ou seu próprio código).

Na presença de vários `DiagnosticSource` ouvintes para a mesma fonte, é suficiente para apenas um ouvinte aceitar o evento, portanto `IsEnabled` não é garantido para ser chamado,

## <a name="next-steps"></a>Próximos passos

* [Correlação de Application Insights](../azure-monitor/app/correlation.md)
* [Monitore Dependências do Application Insights](../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../azure-monitor/app/custom-operations-tracking.md)
