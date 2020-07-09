---
title: Mensagens do barramento de serviço do Azure – filas, tópicos e assinaturas
description: Este artigo fornece uma visão geral das entidades de mensagens do barramento de serviço do Azure (fila, tópicos e assinaturas).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: deeebf56d6e2f4ccfac37c70170a0d1cb4d272a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119164"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e assinaturas do Barramento de Serviço

O Barramento de Serviço do Microsoft Azure oferece suporte a um conjunto de tecnologias middleware orientado a mensagens, baseado em nuvem, incluindo o serviço de enfileiramento de mensagens confiável e o sistema de mensagens de publicação/assinatura durável. Esses recursos de mensagens "orientadas" podem ser considerados como recursos de mensagens dissociados que dão suporte a cenários de publicação/assinatura, desacoplamento temporal e balanceamento de carga usando a carga de trabalho de mensagens do barramento de serviço. A comunicação desacoplada tem diversas vantagens; por exemplo, clientes e servidores podem se conectar como necessário e executar as operações deles de forma assíncrona.

As entidades de mensagens que formam o núcleo dos recursos do sistema de mensagens agenciado no Barramento de Serviço são filas, tópicos e assinaturas, e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem entrega de mensagem do tipo *FIFO (primeiro a entrar, primeiro a sair)* para um ou mais consumidores concorrentes. Ou seja, os receptores geralmente recebem e processam mensagens na ordem em que foram adicionados à fila, e apenas um consumidor recebe e processa cada mensagem. Um dos principais benefícios da utilização de filas é obter o “desacoplamento temporal” de componentes do aplicativo. Em outras palavras, os produtores (remetentes) e os consumidores (receptores) não precisam enviar e receber mensagens ao mesmo tempo porque as mensagens são armazenadas de forma duradoura na fila. Além disso, o produtor não precisa esperar por uma resposta do consumidor a fim de continuar a processar e enviar mensagens.

Um benefício relacionado é o “nivelamento de carga”, que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes. Em muitos aplicativos, a carga do sistema varia ao longo do tempo; no entanto, o tempo de processamento necessário para cada unidade de trabalho normalmente é constante. A intermediação de produtores e de consumidores de mensagem com uma fila significa que o aplicativo que está consumindo só precisa ser provisionado para poder lidar com a carga média em vez da carga de pico. A profundidade da fila aumentará e diminuirá conforme a carga de entrada variar. Esse recurso economiza dinheiro diretamente com relação à quantidade de infraestrutura necessária para atender à carga do aplicativo. À medida que a carga aumenta, mais processos de trabalho poderão ser adicionados à leitura da fila. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o uso ideal dos computadores de trabalho, mesmo se os computadores de trabalho forem diferentes em relação à capacidade de processamento, já que eles efetuarão pull das mensagens em sua própria taxa máxima. Esse padrão geralmente é chamado de padrão de "consumidor concorrente".

A utilização de filas para intermediar entre produtores e consumidores oferece um acoplamento flexível inerente entre os componentes. Como produtores e consumidores não têm conhecimento uns dos outros, um consumidor poderá ser atualizado sem afetar o produtor.

### <a name="create-queues"></a>Criar filas

Você cria filas usando o [Portal do Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md) ou [modelos do Resource Manager](service-bus-resource-manager-namespace-queue.md). Em seguida, você envia e recebe mensagens usando um objeto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient).

Para aprender rapidamente como criar uma fila, enviar e receber mensagens de e para a fila, consulte os [inícios rápidos](service-bus-quickstart-portal.md) para cada método. Para um tutorial mais aprofundado sobre como usar filas, consulte [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md).

Para um exemplo de trabalho, consulte o [exemplo BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) no GitHub.

### <a name="receive-modes"></a>Modos de recepção

É possível especificar dois modos diferentes nos quais o Barramento de Serviço recebe mensagens: *ReceiveAndDelete* ou *PeekLock*. No modo [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) , a operação Receive é única; ou seja, quando o barramento de serviço recebe a solicitação do consumidor, ele marca a mensagem como sendo consumida e a retorna ao aplicativo consumidor. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários em que o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para reconhecer esse cenário, considere um cenário no qual o consumidor emite a solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Como o Barramento de Serviço marca a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem consumida antes da falha.

No modo [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode), a operação de recebimento se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe a solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem recebida. Quando o Barramento de Serviço vê a chamada a **CompleteAsync**, marca a mensagem como sendo consumida.

Se o aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) na mensagem recebida (em vez do método [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Esse método permite que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um tempo limite associado a um bloqueio e, se houver falha no processamento da mensagem pelo aplicativo da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente (basicamente executando uma operação [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) por padrão).

No caso em que o aplicativo falhar após o processamento da mensagem, mas antes que a solicitação **Complete** seja emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Nesse processo, isso é chamado de processamento *Pelo menos uma vez*; ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar processamento duplicado, será necessária uma lógica adicional no aplicativo para detectar duplicatas, o que poderá ser realizado com base na propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) da mensagem, que permanece constante nas tentativas da entrega. Esse recurso é conhecido como processamento *Exatamente Uma Vez*.

## <a name="topics-and-subscriptions"></a>Tópicos e assinaturas

Em contraste com as filas, em que cada mensagem é processada por um único consumidor, *tópicos* e *assinaturas* fornecem uma forma de comunicação de um para muitos em um padrão de *publicação/assinatura*. Útil para o escalonamento de um grande número de destinatários, cada mensagem publicada é disponibilizada para cada assinatura registrada no tópico. As mensagens são enviadas a um tópico e entregues a uma ou mais assinaturas associadas, dependendo das regras de filtro que puderem ser definidas por assinatura. As assinaturas podem usar filtros adicionais para restringir as mensagens que desejam receber. As mensagens são enviadas a um tópico da mesma forma como são enviadas para uma fila, mas as mensagens não são recebidas diretamente do tópico. Em vez disso, elas são recebidas de assinaturas. Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. As mensagens são recebidas de uma assinatura de forma idêntica à maneira como são recebidas de uma fila.

Por comparação, a funcionalidade de envio de mensagens de uma fila é diretamente mapeada para um tópico e sua funcionalidade de recebimento de mensagens é mapeada para uma assinatura. Entre outras coisas, esse recurso significa que as assinaturas dão suporte aos mesmos padrões descritos anteriormente nesta seção em relação a filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e assinaturas

A criação de um tópico é semelhante à criação de uma fila, conforme descrito na seção anterior. Em seguida, você envia mensagens usando a classe [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient). Para receber mensagens, você cria uma ou mais assinaturas para o tópico. De forma semelhante ao que acontece às filas, as mensagens são recebidas de uma assinatura usando um objeto [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) em vez de um objeto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient). Crie o cliente de assinatura, passando o nome do tópico, o nome da assinatura e (opcionalmente) o modo de recebimento como parâmetros.

Para obter um exemplo funcional completo, confira a [amostra BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) no GitHub.

### <a name="rules-and-actions"></a>Regras e ações

Em muitos cenários, as mensagens com características específicas precisam ser processadas de maneiras diferentes. Para habilitar esse processamento, você pode configurar assinaturas para localizar as mensagens com as propriedades desejáveis e, em seguida, realizar determinadas modificações nessas propriedades. Embora as assinaturas do Barramento de Serviço vejam todas as mensagens enviadas para o tópico, você só poderá copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Essa filtragem é realizada usando filtros de assinatura. Tais modificações são chamadas de *ações de filtro*. Quando uma assinatura é criada, você pode fornecer uma expressão de filtro que opere nas propriedades da mensagem, ambas as propriedades do sistema (por exemplo, **rótulo**) e propriedades de aplicativo personalizadas (por exemplo, **StoreName**). A expressão de filtro SQL é opcional nesse caso; sem uma expressão de filtro SQL, qualquer ação de filtro definida em uma assinatura será executada em todas as mensagens para essa assinatura.

Para um exemplo de trabalho completo, consulte o [exemplo TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no GitHub.

Para saber mais sobre possíveis valores de filtro, consulte a documentação das classes [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2,0 entidades (visualização)

Os aplicativos cliente que se conectam ao barramento de serviço Premium do Azure e utilizando a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem aproveitar as entidades abaixo.

### <a name="queues"></a>Filas

As filas em JMS são semanticamente comparáveis com as filas tradicionais do barramento de serviço discutidas acima.

Para criar uma fila, utilize os métodos abaixo na `JMSContext` classe-

```java
Queue createQueue(String queueName)
```

### <a name="topics"></a>Tópicos

Os tópicos em JMS são semanticamente comparáveis com os tópicos tradicionais do barramento de serviço discutidos acima.

Para criar um tópico, utilize os métodos abaixo na `JMSContext` classe-

```java
Topic createTopic(String topicName)
```

### <a name="temporary-queues"></a>Filas temporárias

Quando um aplicativo cliente requer uma entidade temporária que existe durante o tempo de vida do aplicativo, ele pode usar filas temporárias. Eles são utilizados no padrão de [solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Para criar uma fila temporária, utilize os métodos abaixo na `JMSContext` classe-

```java
TemporaryQueue createTemporaryQueue()
```

### <a name="temporary-topics"></a>Tópicos temporários

Assim como as filas temporárias, existem tópicos temporários para habilitar a publicação/assinatura por meio de uma entidade temporária que existe durante o tempo de vida do aplicativo.

Para criar um tópico temporário, utilize os métodos abaixo na `JMSContext` classe-

```java
TemporaryTopic createTemporaryTopic()
```

### <a name="java-message-service-jms-subscriptions"></a>Assinaturas de Java Message Service (JMS)

Embora sejam semanticamente semelhantes às assinaturas descritas acima (ou seja, existem em um tópico e habilitam a semântica de publicação/assinatura), a especificação do serviço de mensagem Java apresenta os conceitos de atributos **compartilhados**, não **compartilhados,** **duráveis** e **não duráveis** para uma determinada assinatura.

> [!NOTE]
> As assinaturas abaixo estão disponíveis na camada Premium do barramento de serviço do Azure para visualização de aplicativos cliente que se conectam ao barramento de serviço do Azure usando a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Para a visualização pública, essas assinaturas não podem ser criadas usando o portal do Azure.
>

#### <a name="shared-durable-subscriptions"></a>Assinaturas duráveis compartilhadas

Uma assinatura durável compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura em todos os momentos.

Como essa é uma assinatura compartilhada, qualquer aplicativo autenticado para receber do barramento de serviço pode receber da assinatura.

Para criar uma assinatura durável compartilhada, use os métodos abaixo na `JMSContext` classe-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A assinatura durável compartilhada continua existindo, a menos que seja excluída usando o `unsubscribe` método na `JMSContext` classe.

```java
void unsubscribe(String name)
```

#### <a name="unshared-durable-subscriptions"></a>Assinaturas duráveis não compartilhadas

Assim como uma assinatura durável compartilhada, uma assinatura durável não compartilhada é usada quando todas as mensagens publicadas em um tópico são recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura em todos os momentos.

No entanto, como essa é uma assinatura não compartilhada, somente o aplicativo que criou a assinatura pode receber dela.

Para criar uma assinatura durável não compartilhada, use os métodos abaixo da `JMSContext` classe- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`No momento, o recurso não tem suporte e foi ignorado.
>

A assinatura durável não compartilhada continua existindo, a menos que seja excluída usando o `unsubscribe` método na `JMSContext` classe.

```java
void unsubscribe(String name)
```

#### <a name="shared-non-durable-subscriptions"></a>Assinaturas não duráveis compartilhadas

Uma assinatura não durável compartilhada é usada quando vários aplicativos cliente precisam receber e processar mensagens de uma única assinatura, somente até que estejam ativamente consumindo/recebendo.

Como a assinatura não é durável, ela não é persistente. As mensagens não são recebidas por essa assinatura quando não há consumidores ativos nela.

Para criar uma assinatura não durável compartilhada, crie uma `JmsConsumer` conforme mostrado nos métodos abaixo da `JMSContext` classe-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A assinatura não durável compartilhada continua existindo até que os consumidores ativos recebam dela.

#### <a name="unshared-non-durable-subscriptions"></a>Assinaturas não duráveis não compartilhadas

Uma assinatura não durável não compartilhada é usada quando o aplicativo cliente precisa receber e processar mensagens de uma assinatura, somente até que esteja ativamente consumindo a partir dela. Somente um consumidor pode existir nessa assinatura, ou seja, o cliente que criou a assinatura.

Como a assinatura não é durável, ela não é persistente. As mensagens não são recebidas por essa assinatura quando não há nenhum consumidor ativo nela.

Para criar uma assinatura não durável não compartilhada, crie uma `JMSConsumer` conforme mostrado nos métodos abaixo da classe ' JMSContext '- 

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`No momento, o recurso não tem suporte e foi ignorado.
>

A assinatura não durável não compartilhada continua existindo até que haja um consumidor ativo recebendo-se dela.

#### <a name="message-selectors"></a>Seletores de mensagem

Assim como os **filtros e as ações** existem para assinaturas regulares do barramento de serviço, existem **seletores de mensagens** para assinaturas JMS.

Os seletores de mensagens podem ser configurados em cada uma das assinaturas JMS e existem como uma condição de filtro nas propriedades do cabeçalho da mensagem. Somente as mensagens com propriedades de cabeçalho correspondentes à expressão do seletor de mensagem são entregues. Um valor nulo ou uma cadeia de caracteres vazia indica que não há um seletor de mensagem para a assinatura/consumidor JMS.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Início rápido: Enviar e receber mensagens usando o portal do Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)


