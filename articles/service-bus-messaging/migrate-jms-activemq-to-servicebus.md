---
title: Migrar aplicativos Java Message Service (JMS) do ActiveMQ para o barramento de serviço do Azure | Microsoft Docs
description: Este artigo explica como migrar aplicativos JMS existentes que interagem com o MQ ativo para interagir com o barramento de serviço do Azure.
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
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122249"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migrar aplicativos Java Message Service (JMS) 2,0 do MQ ativo para o barramento de serviço do Azure

O barramento de serviço do Azure dá suporte a cargas de trabalho Java/J2EE e Spring que utilizam a API do Java Message Service (JMS) 2,0 sobre o protocolo AMQP (protocolo de enfileiramento de mensagens avançado).

Este guia descreve o que você deve estar ciente quando deseja modificar um aplicativo Java Message Service (JMS) 2,0 que interage com um agente JMS (especificamente o Apache ActiveMQ ou Amazon MQ) para interagir com o barramento de serviço do Azure.

## <a name="before-you-start"></a>Antes de começar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Diferenças entre o barramento de serviço do Azure e o Apache ActiveMQ

O barramento de serviço do Azure e o Apache ActiveMQ são agentes de mensagens que estão funcionando como provedores JMS para que os aplicativos cliente enviem mensagens e recebam mensagens do. Ambos habilitam a semântica ponto a ponto com **filas** e semântica de publicação/assinatura com **Tópicos** e **assinaturas**. 

Mesmo assim, há algumas diferenças nos dois.

| Categoria | MQ ativo | Barramento de Serviço do Azure |
| --- | --- | --- |
| Camadas do aplicativo | Monolítico clusterizado | Duas camadas <br> (Gateway + back-end) |
| Suporte a protocolo | <ul> <li>AMQP</li> <li> IGNORAR </li> <li> OpenWire </li> </ul> | AMQP |
| Modo de provisionamento | <ul> <li> IaaS (local) </li> <li> Amazon MQ (PaaS gerenciado) </li> | PaaS gerenciado |
| Tamanho da mensagem | Configurável pelo cliente | 1 MB (camada Premium) |
| Alta disponibilidade | Gerenciado pelo cliente | Gerenciado pela plataforma |
| Recuperação de desastre | Gerenciado pelo cliente | Gerenciado pela plataforma | 

### <a name="current-supported-and-unsupported-features"></a>Recursos com suporte e sem suporte atuais

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Advertências

A natureza em duas camadas do barramento de serviço do Azure proporciona vários recursos de continuidade de negócios (alta disponibilidade e recuperação de desastre). No entanto, há algumas considerações ao utilizar recursos JMS.

#### <a name="service-upgrades"></a>Atualizações de serviço

No caso de atualizações e reinicializações do barramento de serviço, filas ou tópicos temporários serão excluídos.

Se o aplicativo for sensível à perda de dados em filas ou tópicos temporários, é recomendável **não** usar filas ou tópicos temporários e usar filas, tópicos e assinaturas duráveis em vez disso.

#### <a name="data-migration"></a>Migração de dados

Como parte da migração/modificação de seus aplicativos cliente para interagir com o barramento de serviço do Azure, os dados mantidos no ActiveMQ não serão migrados para o barramento de serviço.

Um aplicativo personalizado pode ser necessário para drenar as filas, os tópicos e as assinaturas do ActiveMQ e a reproduzir as mensagens nas filas, nos tópicos e nas assinaturas do barramento de serviço.

#### <a name="authentication-and-authorization"></a>Autenticação e autorização

O RBAC (controle de acesso baseado em função) apoiado pelo Azure ActiveDirectory é o mecanismo de autenticação preferencial para o barramento de serviço do Azure.

No entanto, como o RBAC não tem suporte no momento devido à falta de suporte à autenticação baseada em declarações pelo Apache QPID JMS.

Por enquanto, há suporte para a autenticação somente com chaves SAS.

## <a name="pre-migration"></a>Pré-migração

### <a name="version-check"></a>Verificação de versão

Abaixo estão os componentes utilizados durante a gravação dos aplicativos JMS e as versões específicas com suporte. 

| Componentes | Versão |
|---|---|
| API do Java Message Service (JMS) | 1,1 ou superior |
| Protocolo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Verifique se as portas AMQP estão abertas

O barramento de serviço do Azure dá suporte à comunicação por meio do protocolo AMQP. Para essa finalidade, a comunicação pelas portas 5671 (AMQP) e 443 (TCP) precisa ser habilitada. Dependendo de onde os aplicativos cliente estão hospedados, talvez seja necessário um tíquete de suporte para permitir a comunicação por essas portas.

> [!IMPORTANT]
> O barramento de serviço do Azure dá suporte **apenas** ao protocolo AMQP 1,0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Configurar configurações corporativas (VNET, firewall, ponto de extremidade privado, etc.)

O barramento de serviço do Azure permite vários recursos de segurança e alta disponibilidade da empresa. Para saber mais sobre eles, siga os links de documentação abaixo.

  * [Pontos de extremidade de serviço de rede virtual](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Criptografia do lado do serviço com BYOK (chave gerenciada pelo cliente)](configure-customer-managed-key.md)
  * [Pontos de extremidade privados](private-link-service.md)
  * [Autenticação e autorização](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitoramento, alertas e rastreamento

As métricas são publicadas para cada namespace do barramento de serviço em Azure Monitor e podem ser aproveitadas para alertas e dimensionamento dinâmico de recursos alocados para o namespace.

Leia mais sobre as diferentes métricas e como configurar alertas nelas em [métricas do barramento de serviço em Azure monitor](service-bus-metrics-azure-monitor.md).

Além disso, leia mais sobre [rastreamento do lado do cliente para operações de dados](service-bus-end-to-end-tracing.md) e [log operacional/de diagnóstico para operações de gerenciamento](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Métricas-NewRelic

Veja abaixo um guia prático sobre quais métricas do ActiveMQ são mapeadas para quais métricas no barramento de serviço do Azure. As referências a seguir são referenciadas em NewRelic.

  * [Métricas do ActiveMQ/Amazon MQ NewRelic](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Métricas de NewRelic do barramento de serviço do Azure](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Atualmente, o NewRelic não tem uma integração direta com o ActiveMQ diretamente, mas tem métricas disponíveis para o Amazon MQ.
> Como o Amazon MQ é derivado de ActiveMQ, o guia abaixo mapeia as métricas do NewRelic do AmazonMQ para o barramento de serviço do Azure.
>

|Agrupamento de métricas| Métrica AmazonMQ/active MQ | Métrica do barramento de serviço do Azure |
|------------|---------------------------|--------------------------|
|Agente|`CpuUtilization`|`CPUXNS`|
|Agente|`MemoryUsage`|`WSXNS`|
|Agente|`CurrentConnectionsCount`|`activeConnections`|
|Agente|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Agente|`InactiveDurableTopicSubscribersCount`|Aproveitar as métricas de assinatura|
|Agente|`TotalMessageCount`|Aproveitar o nível de fila/tópico/assinatura`activeMessages`|
|Fila/tópico|`EnqueueCount`|`incomingMessages`|
|Fila/tópico|`DequeueCount`|`outgoingMessages`|
|Fila|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migração

Para migrar seu aplicativo JMS 2,0 existente para interagir com o barramento de serviço do Azure, as etapas a seguir precisam ser executadas.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Exportar a topologia de ActiveMQ e criar as entidades no barramento de serviço do Azure (opcional)

Para garantir que os aplicativos cliente possam se conectar diretamente ao barramento de serviço do Azure, a topologia, que inclui filas, tópicos e assinaturas, precisa ser migrada do **Apache ActiveMQ** para o **barramento de serviço do Azure**.

> [!NOTE]
> Para aplicativos Java Message Service (JMS), a criação de filas, tópicos e assinaturas é uma operação de tempo de execução. A maioria dos provedores de Java Message Service (JMS) (agentes de mensagens) oferecem a funcionalidade para criar *filas*, *Tópicos* e *assinaturas* em tempo de execução.
>
> Portanto, a etapa acima é opcional.
>
> Para garantir que seu aplicativo tenha as permissões para criar a topologia em tempo de execução, verifique se a cadeia de conexão com as permissões de ***SAS ' Manage '*** é usada.

Para fazer isto 
  * Aproveite as [ferramentas de linha de comando do ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) para exportar a topologia
  * Recriar a mesma topologia usando um [modelo de Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Execute o modelo de Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importar a dependência do Maven para implementação JMS do barramento de serviço

Para garantir a conectividade direta com o barramento de serviço do Azure, o pacote ***Azure-ServiceBus-JMS*** precisa ser adicionado como uma dependência para o arquivo Maven, `pom.xml` como mostrado abaixo.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Alterações de configuração do servidor de aplicativos

Essa parte é personalizada para o servidor de aplicativos que está hospedando seus aplicativos cliente se conectando ao Active MQ.

#### <a name="tomcat"></a>Tomcat

Aqui, começamos com a configuração específica para o MQ ativo, conforme mostrado no `/META-INF/context.xml` arquivo.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

que pode ser adaptado como abaixo para apontar para o barramento de serviço do Azure

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Aplicativos Spring

##### <a name="update-applicationproperties-file"></a>Atualizar `application.properties` arquivo

Se estiver usando um aplicativo Spring boot para se conectar ao ActiveMQ.

Aqui, o objetivo é ***remover*** as propriedades específicas do ActiveMQ do `application.properties` arquivo.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Em seguida, ***adicione*** as propriedades específicas do barramento de serviço ao `application.properties` arquivo.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Substitua o ActiveMQConnectionFactory por ServiceBusJmsConnectionFactory

A próxima etapa é substituir a instância de ActiveMQConnectionFactory por ServiceBusJmsConnectionFactory.

> [!NOTE] 
> As alterações de código reais são específicas para o aplicativo e como as dependências são gerenciadas, mas o exemplo abaixo fornece a orientação sobre ***o que*** deve ser alterado.
>

Anteriormente, você pode estar instanciando um objeto da ActiveMQConnectionFactory como mostrado abaixo.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Isso seria alterado para a instanciação de um objeto do ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Após a migração

Agora que você modificou o aplicativo para iniciar o envio e recebimento de mensagens do barramento de serviço do Azure, você deve verificar se ela funciona conforme o esperado. Quando isso for feito, você poderá continuar refinando e modernizando sua pilha de aplicativos.

## <a name="next-steps"></a>Próximas etapas

Aproveite o [iniciador do Spring boot para o barramento de serviço do Azure JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) para integração direta com o barramento de serviço do Azure.

Para saber mais sobre as mensagens do barramento de serviço e o Java Message Service (JMS), consulte os seguintes tópicos:

* [Barramento de serviço JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
