---
title: Visão geral de sistema de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível do Barramento de Serviço do Azure, um agente de mensagem de integração empresarial totalmente gerenciado.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396293"
---
# <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?

O Barramento de Serviço do Microsoft Azure é um agente de mensagens de integração empresarial totalmente gerenciado. O Barramento de Serviço pode separar aplicativos e serviços. O Barramento de Serviço oferece uma plataforma confiável e segura para a transferência assíncrona de dados e estados.

Os dados são transferidos entre diferentes aplicativos e serviços usando *mensagens*. Uma mensagem é em formato binário e pode conter JSON, XML ou apenas texto. Para saber mais, confira [Integration Services](https://azure.com/integration).

Alguns cenários de sistema de mensagens comuns são:

* *Mensagens*. Transfira dados comerciais, como ordens de venda ou compra, diários ou movimentos de estoque.
* *Separar aplicativos*. Melhore a confiabilidade e a escalabilidade de aplicativos e serviços. O cliente e o serviço não precisam estar online ao mesmo tempo.
* *Tópicos e assinaturas*. Habilite as relações 1:*n* entre publicadores e assinantes.
* *Sessões de mensagem*. Implemente fluxos de trabalho que exijam ordenação ou adiamento de mensagens.

## <a name="namespaces"></a>Namespaces

Um namespace é um contêiner para todos os componentes de mensagem. Vários tópicos e filas podem estar em um único namespace, e os namespaces geralmente servem como contêineres de aplicativos.

## <a name="queues"></a>Filas

As mensagens são enviadas e recebidas a partir de *filas*. As filas armazenam mensagens até que o aplicativo de recebimento esteja disponível para recebê-las e processá-las.

![Fila](./media/service-bus-messaging-overview/about-service-bus-queue.png)

As mensagens em filas são ordenadas e recebem carimbo de data/hora na chegada. Quando aceita, a mensagem é mantida protegida em armazenamento com redundância. As mensagens são entregues em modo *pull*, e somente quando solicitado.

## <a name="topics"></a>Tópicos

Também é possível usar *tópicos* para enviar e receber mensagens. Enquanto uma fila é frequentemente usada para comunicação ponto a ponto, os tópicos são úteis em cenários de publicação/assinatura.

![Tópico](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Os tópicos podem ter várias assinaturas independentes. Um assinante de um tópico pode receber uma cópia de cada mensagem enviada para esse tópico. As assinaturas são entidades nomeadas. As assinaturas persistem, mas podem expirar ou se autoexcluir.

Talvez você não queira que assinaturas individuais recebam todas as mensagens enviadas para um tópico. Nesse caso, é possível usar *regras* e *filtros* para definir condições que disparem *ações* opcionais. Você pode filtrar mensagens especificadas e definir ou modificar as propriedades da mensagem. Para saber mais, confira [Filtros e ações de tópico](topic-filters.md).

## <a name="advanced-features"></a>Recursos avançados

O Barramento de Serviço inclui recursos avançados que permitem resolver problemas de mensagens mais complexos. As seções a seguir descrevem vários desses recursos.

### <a name="message-sessions"></a>Sessões de mensagem

Para criar uma garantia PEPS (primeiro a entrar, primeiro a sair) no Barramento de Serviço, use as sessões. As sessões de mensagem permitem manipulação ordenada e conjunta de sequências não associadas de mensagens relacionadas. Para saber mais, confira [Sessões de mensagem: PEPS (primeiro a entrar, primeiro a sair)](message-sessions.md).

### <a name="autoforwarding"></a>Encaminhamento automático

O recurso de encaminhamento automático encadeia uma fila ou assinatura em outra fila ou outro tópico. Eles devem fazer parte do mesmo namespace. Com o encaminhamento automático, o Barramento de Serviço remove automaticamente mensagens de uma filha ou assinatura e as coloca em um outra fila ou outro tópico. Para saber mais, confira [Encadeamento das entidades do Barramento de Serviço com encaminhamento automático](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fila de mensagens mortas

O Barramento de Serviço dá suporte a uma DLQ (fila de mensagens mortas). Uma DLQ contém mensagens que não podem ser entregues a um receptor. Ela contém mensagens que não podem ser processadas. O Barramento de Serviço permite remover mensagens da DLQ e inspecioná-las. Para saber mais, confira [Visão geral das filas de mensagens mortas do Barramento de Serviço](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Entrega agendada

Você pode enviar mensagens para uma fila ou um tópico para processamento atrasado. É possível agendar um trabalho para ser disponibilizado para processamento por um sistema em um determinado momento. Para saber mais, confira [Mensagens agendadas](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Adiamento de mensagens

Um cliente de fila ou assinatura pode adiar a recuperação de uma mensagem para mais tarde. Esse adiamento pode ser devido a circunstâncias especiais no aplicativo. A mensagem permanece na fila ou assinatura, mas é reservada. Para saber mais, confira [Adiamento de mensagem](message-deferral.md).

### <a name="batching"></a>Envio em lote

O envio em lote do lado do cliente permite que um cliente de tópico ou fila adie o envio de uma mensagem por um determinado período. Se o cliente enviar mensagens adicionais durante esse período, ele transmitirá as mensagens em um único lote. Para saber mais, confira [Envio em lote do lado do cliente](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transactions

Uma transação agrupa duas ou mais operações em um *escopo de execução*. O Barramento de Serviço dá suporte a operações de agrupamento em uma única entidade de mensagens no escopo de uma única transação. Uma entidade de mensagem pode ser uma fila, um tópico ou uma assinatura. Para saber mais, confira [Visão geral do processamento de transações do Barramento de Serviço](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtragem e ações

Os assinantes podem definir quais mensagens desejam receber de um tópico. Essas mensagens são especificadas na forma de uma ou mais regras de assinatura nomeadas. Para cada condição de regra com correspondência, a assinatura produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra com correspondência. Para saber mais, confira [Filtros e ações de tópico](topic-filters.md).

### <a name="autodelete-on-idle"></a>Exclusão automática em tempo ocioso

A exclusão automática em tempo ocioso permite que você especifique um intervalo de tempo ocioso, após o qual uma fila será excluída automaticamente. A duração mínima é de 5 minutos. Para saber mais, confira a [Propriedade QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Detecção de duplicidade

Um erro pode fazer com que o cliente tenha uma dúvida sobre o resultado de uma operação de envio. A detecção de duplicidades permite que o remetente reenvie a mesma mensagem. Outra opção é que a fila ou o tópico descarte cópias duplicadas. Para saber mais, confira [Detecção de duplicatas](duplicate-detection.md).

### <a name="security-protocols"></a>Protocolos de segurança
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

O Barramento de Serviço dá suporte a protocolos de segurança, como [Assinaturas de Acesso Compartilhado](service-bus-sas.md) (SAS), [Controle de Acesso Baseado em Função](authenticate-application.md) (RBAC) e [Identidades Gerenciadas para recursos do Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Recuperação de desastre geográfico

Quando os datacenters ou regiões do Azure passam por um tempo de inatividade, a recuperação de desastre geográfico permite que o processamento de dados continue operando em um datacenter ou região diferente. Para mais informações consulte [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](service-bus-geo-dr.md).

### <a name="security"></a>Segurança

O Barramento de Serviço dá suporte aos protocolos padrão [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Bibliotecas de cliente

O Barramento de Serviço dá suporte a bibliotecas de clientes para [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master) e [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integração

O Barramento de Serviço integra-se totalmente aos serviços do Azure a seguir:

* [Grade de Eventos](https://azure.microsoft.com/services/event-grid/)
* [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o sistema de mensagens do Barramento de Serviço, consulte os artigos a seguir:

* Para comparar os serviços de mensagens do Azure, confira [Comparação de serviços](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Teste os inícios rápidos para [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Para gerenciar recursos do Barramento de Serviço, confira [Explorador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Para saber mais sobre as camadas Standard e Premium e respectivos preços, confira [Preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* Para saber mais sobre o desempenho e a latência da camada Premium, confira [Mensagens Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
