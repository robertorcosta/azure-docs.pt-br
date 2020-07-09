---
title: Usar o AMQP com a API do serviço de mensagens Java & barramento de serviço do Azure
description: Como usar o Java Message Service (JMS) com o Barramento de Serviço do Microsoft Azure e Advanced Message Queuing Protocol (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119150"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Usar o Java Message Service (JMS) com o barramento de serviço do Azure e o AMQP 1,0

O suporte para o protocolo **Advanced Message Queuing Protocol (AMQP) 1,0** no barramento de serviço do Azure significa que você pode usar o enfileiramento e publicar/assinar recursos de mensagens orientadas de uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos formados por componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

Este artigo explica como usar os recursos de mensagens do barramento de serviço do Azure (tópicos de filas e publicação/assinatura) de aplicativos Java usando a API **JMS (Java Message Service)** popular sobre o protocolo AMQP.

## <a name="get-started-with-service-bus"></a>Introdução ao Barramento de serviço
Este guia pressupõe que você já tenha um namespace do barramento de serviço que contém uma fila chamada `basicqueue` . Se não tiver, você poderá [criar o namespace e a fila](service-bus-create-namespace-portal.md) usando o [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar namespaces e filas do Barramento de Serviço, consulte [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Filas e tópicos particionados também dão suporte ao AMQP. Para saber mais, confira [Entidades de mensagens particionadas](service-bus-partitioning.md) e [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Quais recursos JMS têm suporte?

Aqui estão os recursos de JMS com suporte no barramento de serviço do Azure.

| Recursos | Camada standard do barramento de serviço do Azure-JMS 1,1 | Camada Premium do barramento de serviço do Azure-JMS 2,0 (visualização) |
|---|---|---|
| Criação automática de entidades em AMQP | Sem suporte | **Com suporte** |
| Filas | **Com suporte** | **Com suporte** |
| Tópicos | **Com suporte** | **Com suporte** |
| Filas temporárias | Sem suporte <br/> (Em vez disso, crie uma fila regular com *AutoDeleteOnIdle* definido) | **Com suporte** |
| Tópicos temporários | Sem suporte | **Com suporte** |
| Seletores de mensagem | Sem suporte | **Com suporte** |
| Navegadores de fila | Sem suporte <br/> (Use a funcionalidade de *Peek* da API do barramento de serviço) | **Com suporte** |
| Assinaturas duráveis compartilhadas | **Com suporte** | **Com suporte**|
| Assinaturas duráveis não compartilhadas | Sem suporte | **Com suporte** |
| Assinaturas não duráveis compartilhadas | Sem suporte | **Com suporte** |
| Assinaturas não duráveis não compartilhadas | Sem suporte | **Com suporte** |
| Cancelar assinatura de assinaturas duráveis | Sem suporte | **Com suporte** |
| ReceiveNoWait | Sem suporte | **Com suporte** |
| Transações distribuídas | Sem suporte | Sem suporte |
| Terminal durável | Sem suporte | Sem suporte |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Limitações adicionais para a camada standard do barramento de serviço
Apenas um **MessageProducer** ou **MessageConsumer** é permitido por **Sessão**. Se precisar criar vários **MessageProducers** ou **MessageConsumers** em um aplicativo, crie uma **Session** dedicada para cada um deles.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Baixando a biblioteca de cliente Java Message Service (JMS)

Para se conectar com o barramento de serviço do Azure e aproveitar a API JMS (Java Message Service) em AMQP, as bibliotecas abaixo precisam ser aproveitadas. Eles devem ser adicionados ao caminho de compilação usando a ferramenta de gerenciamento de dependência preferencial para seu projeto.

A biblioteca de cliente necessária depende de qual tipo de preço é usado.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Camada Premium-JMS 2,0 sobre AMQP (versão prévia)

Para utilizar todos os recursos de visualização disponíveis na camada Premium do barramento de serviço do Azure, utilize a biblioteca [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) .

### <a name="standard-tier---jms-11-over-amqp"></a>Camada Standard-JMS 1,1 sobre AMQP

Para utilizar os recursos JMS com suporte na camada standard do barramento de serviço (veja [quais recursos JMS têm suporte?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) Utilize as bibliotecas abaixo-

* [Especificação Geronimo JMS 1,1](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Cliente QPID JMS](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Os nomes e versões do JMS JAR podem ter mudado. Para obter detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Codificando os aplicativos Java

Depois que as dependências forem importadas, os aplicativos Java podem ser escritos de maneira independente do provedor JMS.

Como o barramento de serviço do Azure Standard e Premium diferem nas dependências e no número de recursos JMS que eles dão suporte, o modelo de programação é ligeiramente diferente para os dois.

> [!IMPORTANT]
> O guia abaixo demonstra como se conectar ao barramento de serviço do Azure, dado um aplicativo simples.
>
> Considerando que a maioria das arquiteturas de aplicativos empresariais pode ter uma maneira personalizada de gerenciar dependências e configurações, use o abaixo como guia para entender o que é necessário e se adaptar ao seu aplicativo adequadamente.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Conectando-se ao barramento de serviço do Azure usando JMS

Para se conectar com o barramento de serviço do Azure usando clientes JMS, você precisa do **ConnectionString** que está disponível nas ' políticas de acesso compartilhado ' no [portal do Azure](https://portal.azure.com) na **cadeia de conexão primária**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Conectando-se ao barramento de serviço do Azure Premium sobre JMS 2,0 (visualização)

1. Criar uma instância do`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instancie o `ServiceBusJmsConnectionFactory` com o apropriado `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    ou um `JMSContext` (para clientes JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Conectando-se ao padrão do barramento de serviço do Azure em JMS 1,1

1. Insira a configuração do barramento de serviço do Azure no arquivo de propriedades JNDI chamado **ServiceBus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Configurar contexto JNDI e configure o ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Escrever o aplicativo JMS

Depois que o `Session` ou `JMSContext` tiver sido instanciado, seu aplicativo poderá aproveitar as APIs JMS familiares para executar operações de gerenciamento e de dados.

Consulte a lista de [recursos JMS com suporte](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) para a camada Standard e Premium para ver quais APIs terão suporte em cada camada.

## <a name="summary"></a>Resumo
Este guia demonstra como os aplicativos cliente Java que usam o Java Message Service (JMS) sobre o AMQP 1,0 podem interagir com o barramento de serviço do Azure.

Você também pode usar o AMQP 1.0 do Service Bus de outras linguagens, incluindo .NET, C, Python e PHP. Os componentes criados com essas diferentes linguagens podem trocar mensagens de forma confiável e com total fidelidade usando o suporte do AMQP 1.0 no Service Bus.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o barramento de serviço do Azure e detalhes sobre as entidades JMS (Java Message Service), confira os links abaixo- 
* [Barramento de serviço-filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
* [Barramento de serviço-entidades de serviço de mensagem Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](service-bus-amqp-overview.md)
* [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](service-bus-amqp-dotnet.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

