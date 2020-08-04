---
title: Usar um hub de eventos do aplicativo Apache Kafka – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre a compatibilidade do Apache Kafka com os Hubs de Eventos do Azure.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ab29c9c4270514e95752ab2bbd085ffe1b0a2fb0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534865"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usar Hubs de Eventos do Azure de aplicativos Apache Kafka
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Kafka existentes como uma alternativa para executar seu próprio cluster Kafka. Os Hubs de Eventos dão suporte ao [protocolo 1.0 e posterior do Apache Kafka](https://kafka.apache.org/documentation/) e funciona com aplicativos existentes do Kafka, incluindo o MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Hubs de eventos para o Kafka oferece?

Os Hubs de eventos para o recurso de Kafka fornece um cabeçalho de protocolo na parte superior de Hubs de eventos do Azure que é binário compatível com versões do Kafka 1.0 e posterior para tanto leitura e gravação para os tópicos do Kafka. Você pode começar a usar o ponto de extremidade do Kafka dos aplicativos sem alteração de código, mas com uma alteração mínima de configuração. Você atualiza a cadeia de conexão nas configurações para apontar para o ponto de extremidade do Kafka exposto pelo hub de eventos, em vez de apontar para o cluster do Kafka. Em seguida, você poderá começar a transmitir eventos dos aplicativos que usam o protocolo Kafka nos Hubs de Eventos. Essa integração também dá suporte a estruturas como [Kafka conectar](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), que está atualmente em visualização. 

Os hubs de eventos e Kafka conceitualmente são quase idênticos: ambos são logs particionados criados para streaming de dados. A tabela a seguir mapeia conceitos entre Kafka e Hubs de Eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceitual de Kafka e Hub de Eventos

| Conceito de Kafka | Conceito de Hubs de Eventos|
| --- | --- |
| Cluster | Namespace |
| Tópico | Hub de evento |
| Partition | Partition|
| Grupo de Consumidores | Grupo de Consumidores |
| Deslocamento | Deslocamento|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e Hubs de Eventos

Embora o [Kafka Apache](https://kafka.apache.org/) seja um software, que pode ser executado sempre que você quiser, os Hubs de Eventos são um serviço de nuvem semelhante ao Armazenamento de Blobs do Azure. Não há nenhum servidor ou rede para gerenciar e nenhum agente para configurar. Crie um namespace, que é um FQDN no qual seus tópicos residem e crie Hubs de Eventos ou tópicos dentro desse namespace. Para obter mais informações sobre namespaces e Hubs de Eventos, consulte [Recursos de Hubs de Eventos](event-hubs-features.md#namespace). Como um serviço de nuvem, os hubs de eventos usam um único endereço IP virtual estável como o ponto de extremidade, para que os clientes não precisem saber sobre os agentes ou computadores em um cluster. 

A escala em Hubs de Eventos é controlada pelo número de unidades de produtividade que você adquirir, com cada unidade de produtividade lhe dando direito a 1 MB por segundo ou 1000 eventos por segundo de entrada. Por padrão, os Hubs de Eventos ampliam as unidades de taxa de transferência quando você atinge seu limite com o recurso [Inflação Automática](event-hubs-auto-inflate.md); esse recurso também funciona com o recurso Hubs de Eventos para Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação
Sempre que você publica ou consome eventos de um hub de eventos para Kafka, seu cliente está tentando acessar os recursos dos hubs de eventos. Você deseja garantir que os recursos sejam acessados usando uma entidade autorizada. Ao usar Apache Kafka protocolo com seus clientes, você pode definir sua configuração para autenticação e criptografia usando os mecanismos SASL. Ao usar os hubs de eventos para o Kafka, é necessário ter criptografia TLS (pois todos os dados em trânsito com hubs de eventos são criptografados como TLS). Isso pode ser feito especificando a opção SASL_SSL no arquivo de configuração. 

Os hubs de eventos do Azure fornecem várias opções para autorizar o acesso aos seus recursos seguros. 

- OAuth
- Assinatura de acesso compartilhado (SAS)

#### <a name="oauth"></a>OAuth
Os hubs de eventos se integram com o Azure Active Directory (Azure AD), que fornece um servidor de autorização centralizada compatível com **OAuth** 2,0. Com o Azure AD, você pode usar o RBAC (controle de acesso baseado em função) para conceder permissões refinadas às suas identidades de cliente. Você pode usar esse recurso com os clientes do Kafka especificando **SASL_SSL** para o protocolo e **OAUTHBEARER** para o mecanismo. Para obter detalhes sobre as funções e os níveis do Azure para o acesso ao escopo, consulte [autorizar o acesso com o Azure ad](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>SAS (Assinatura de Acesso Compartilhado)
Os hubs de eventos também fornecem as **SAS (assinaturas de acesso compartilhado)** para acesso delegado aos hubs de eventos para recursos Kafka. A autorização de acesso usando o mecanismo baseado em token do OAuth 2,0 fornece segurança superior e facilidade de uso sobre SAS. As funções internas também podem eliminar a necessidade de autorização baseada em ACL, que deve ser mantida e gerenciada pelo usuário. Você pode usar esse recurso com os clientes do Kafka especificando **SASL_SSL** para o protocolo e **sem formatação** para o mecanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Ao usar a autenticação SAS com clientes Kafka, as conexões estabelecidas não são desconectadas quando a chave SAS é regenerada. 

#### <a name="samples"></a>Exemplos 
Para obter um **tutorial** com as instruções detalhadas para criar um hub de eventos e acessá-lo usando SAS ou OAuth, consulte [início rápido: streaming de dados com hubs de eventos usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para obter mais **exemplos** que mostram como usar o OAuth com hubs de eventos para Kafka, consulte [exemplos no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos de Hubs de Eventos disponíveis para Kafka

O recurso Hub de eventos para Kafka permite que você escreva com um protocolo e leia com outro, para que seus produtores atuais do Kafka possam continuar publicando via Kafka e você pode adicionar leitores a Hubs de eventos, como o Azure Stream Analytics ou o Azure Functions. Além disso, os recursos do Event Hubs, como [Capture](event-hubs-capture-overview.md) e [Geo Disaster-Recovery](event-hubs-geo-dr.md), também funcionam com o recurso Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Recursos que ainda não são suportados 

Aqui está a lista de recursos de Kafka que ainda não têm suporte:

*   Transação
*   Compactação
*   Retenção baseada em tamanho
*   Compactação do log
*   Suporte a API de Kafta HTTP
*   Kafka Streams

## <a name="next-steps"></a>Próximas etapas
Este artigo forneceu uma introdução aos Hubs de Eventos para Kafka. Para saber mais, confira [Guia do desenvolvedor do Apache Kafka para Hubs de Eventos do Azure](apache-kafka-developer-guide.md).


