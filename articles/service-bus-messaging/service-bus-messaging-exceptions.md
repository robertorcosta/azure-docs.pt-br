---
title: Guia de solução de problemas do barramento de serviço do Azure | Microsoft Docs
description: Lista de exceções de mensagens do Barramento de Serviço e ações sugeridas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: eebbef25f2cd4539a5092f271c3944c24503f287
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156804"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de solução de problemas do barramento de serviço do Azure
Este artigo fornece algumas das exceções .NET geradas pelo barramento de serviço .NET Framework APIs e também outras dicas para solucionar problemas. 

## <a name="service-bus-messaging-exceptions"></a>Exceções de mensagem do Barramento de Serviço
Esta seção lista as exceções .NET geradas por .NET Framework APIs. 

### <a name="exception-categories"></a>Categorias de exceções
As APIs de mensagens geram exceções que podem se enquadrar nas categorias a seguir, junto com a ação associada que pode ser tomada para tentar corrigi-las. O significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens:

1. Erro de codificação do usuário ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de prosseguir.
2. Erro de instalação/configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: examinar sua configuração e alterá-la, se necessário.
3. Exceções temporárias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: repetir a operação ou notificar os usuários. A classe `RetryPolicy` no SDK do cliente pode ser configurada para tratar repetições automaticamente. Para obter mais informações, consulte [diretrizes de repetição](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir: 

### <a name="exception-types"></a>Tipos de exceção
A tabela a seguir relaciona os tipos de mensagens de exceção e suas causas e aponta a ação sugerida que você pode tomar.

| **Tipo de exceção** | **Descrição/Causa/Exemplos** | **Ação sugerida** | **Observação sobre repetição automática/imediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente, se necessário. Confira [TimeoutException](#timeoutexception). |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de usuário solicitada não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [Concluir()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gerará essa exceção se a mensagem tiver sido recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode). |Verifique o código e a documentação. Verifique se a operação solicitada é válida. |A repetição não ajuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. |Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. |A repetição não ajuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. |Verifique se o provedor de token for criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. |Uma nova tentativa pode ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento(s) de caminho.<br /> O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |A repetição não ajuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A entidade associada à operação não existe ou foi excluída. |Certifique-se de que a entidade existe. |A repetição não ajuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativa para receber uma mensagem com um número de sequência específico. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não tenha sido recebida. Verifique a fila de mensagens mortas para ver se a mensagem foi colocada ali. |A repetição não ajuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |O cliente não é capaz de estabelecer uma conexão com o barramento de serviço. |Verifique se o nome de host fornecido está correto e se o host está acessível. |Tentar novamente poderá ajudar se houver problemas intermitentes de conectividade. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |O serviço não é capaz de processar a solicitação no momento. |O cliente pode esperar um período de tempo e repetir a operação. |O cliente pode tentar novamente após um intervalo determinado. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição de tal exceção. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |O token de bloqueio associado à mensagem expirou ou o token de bloqueio não foi encontrado. |Descartar a mensagem. |A repetição não ajuda. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |O bloqueio associado a esta sessão foi perdido. |Anular o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |A repetição não ajuda. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |A exceção de mensagens genéricas pode ser lançada nos seguintes casos:<br /> Uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico).<br />  A tentativa de TAn é feita para enviar uma mensagem maior que 256 KB. O servidor ou o serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Geralmente é uma exceção transitória. |Verifique o código e certifique-se de que somente objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). Verifique a documentação para os tipos de valor com suporte das propriedades e somente os tipos de uso com suporte. Verifique a propriedade [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se for **true**, você poderá repetir a operação. |O comportamento de repetição é indefinido e pode não ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade no namespace de serviço. |Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. |A repetição não ajuda. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu o tamanho máximo permitido ou o número máximo de conexões com um namespace foi excedido. |Crie espaço na entidade ao receber mensagens da entidade ou de suas subfilas. Confira [QuotaExceededException](#quotaexceededexception). |Tentar novamente pode ajudar se as mensagens foram removidas nesse meio tempo. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |O Barramento de Serviço retorna essa exceção se você tentar criar uma ação de regra inválida. Barramento de Serviço anexa essa exceção a uma mensagem morta se ocorrer um erro ao processar a ação de regra para a mensagem. |Verifique se a ação da regra está correta. |A repetição não ajuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |O Barramento de Serviço retorna essa exceção se você tentar criar um filtro inválido. O Barramento de Serviço anexa essa exceção a uma mensagem morta se ocorreu um erro ao processar o filtro para a mensagem. |Verifique se o filtro está correto. |A repetição não ajuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentativa de aceitar uma sessão com uma ID de sessão específica, mas a sessão está atualmente bloqueada por outro cliente. |Verifique se a sessão foi desbloqueada por outros clientes. |Tentar novamente pode ajudar se a sessão foi liberada nesse meio tempo. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Muitas operações fazem parte da transação. |Reduza o número de operações que fazem parte dessa transação. |A repetição não ajuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Solicitação para uma operação de runtime em uma entidade desabilitada. |Ativar a entidade. |Tentar novamente poderá ajudar se a entidade tiver sido ativada durante o processo. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |O Barramento de Serviço retornará essa exceção se você enviar uma mensagem para um tópico que tem a pré-filtragem habilitada e se nenhum dos filtros for compatível. |Verifique se pelo menos um filtro é compatível. |A repetição não ajuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |O conteúdo de uma mensagem excede o limite de 256 KB. O limite de 256 KB é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. |Reduza o tamanho da carga de mensagem e repita a operação. |A repetição não ajuda. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação de ambiente (*Transaction.Current*) é inválida. Ela pode ter sido concluída ou anulada. A exceção interna pode fornecer informações adicionais. | |A repetição não ajuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Uma operação é tentada em uma transação incerta, ou uma tentativa é feita para confirmar a transação e ela se torna incerta. |Seu aplicativo deve tratar essa exceção (como um caso especial), visto que a transação pode já ter sido confirmada. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que uma cota para uma entidade específica foi excedida.

#### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, geralmente é o tamanho da fila. A propriedade de mensagem de erro contém mais detalhes, como no exemplo a seguir:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem informa que o tópico excedeu seu limite de tamanho, neste caso 1 GB (o limite de tamanho padrão). 

#### <a name="namespaces"></a>Namespaces

Para namespaces, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que um aplicativo excedeu o número máximo de conexões com um namespace. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causas comuns
Há duas causas comuns desse erro: a fila de mensagens mortas e os destinatários de mensagem não estão funcionando.

1. **[Fila de mensagens mortas](service-bus-dead-letter-queues.md)** Um leitor não pode concluir as mensagens e as mensagens são retornadas para fila/tópico quando o bloqueio expira. Isso pode acontecer se o leitor encontrar uma exceção que impeça a chamada de [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois que uma mensagem foi lida 10 vezes, ela é movida para a fila de mensagens mortas por padrão. Esse comportamento é controlado pela propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e tem um valor padrão de 10. Conforme as mensagens são acumuladas na fila de mensagens mortas, elas ocupam espaço.
   
    Para resolver o problema, leia e conclua as mensagens da fila de mensagens mortas, como faria em qualquer outra fila. Você pode usar o método [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) para ajudar a formatar o caminho da fila de mensagens mortas.
2. **Receptor parado**. Um receptor parou de receber mensagens de uma fila ou assinatura. A maneira de identificar isso é examinar a propriedade [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , que mostra a análise completa das mensagens. Se a propriedade [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) for alta ou em crescimento, as mensagens não serão lidas tão rapidamente quanto estão sendo gravadas.

### <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Você deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), pois atingir esse limite também pode causar um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado na propriedade [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings), como parte da cadeia de conexão, ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A mensagem de erro pode variar, mas ele sempre contém o valor de tempo limite especificado para a operação atual. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. Dependendo da biblioteca que você usa, outras portas também são usadas. Por exemplo: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço e, em seguida, você poderá verificar quantos com êxito/falha e também ver a latência de conexão TCP. Você pode baixar a ferramenta de `psping` [aqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q yournamespace.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc`, `ping`e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e as analisarem ou entrem em contato com a [suporte da Microsoft](https://support.microsoft.com/).


## <a name="next-steps"></a>Próximos passos

Para obter a referência completa da API do .NET de Barramento de Serviço, consulte a [Referência de API do .NET do Azure](/dotnet/api/overview/azure/service-bus).

Para saber mais sobre o [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/), veja estes artigos:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Arquitetura do Barramento de Serviço](service-bus-architecture.md)

