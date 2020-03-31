---
title: Azure Service Bus premium e níveis padrão
description: Este artigo descreve os níveis padrão e premium do Azure Service Bus. Compara esses níveis e fornece diferenças técnicas.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774560"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço

O Sistema de Mensagens do Barramento de Serviço, que inclui entidades como filas e tópicos, combina recursos corporativos de mensagens com rica semântica de assinatura para publicação na escala de nuvem. O Sistema de Mensagens do Barramento de Serviço é usado como o backbone de comunicação para muitas soluções de nuvem sofisticadas.

O nível *Premium* de Mensagens de Barra de Serviço aborda solicitações comuns de clientes em escala, desempenho e disponibilidade para aplicativos de missão crítica. A camada Premium é recomendada para cenários de produção. Embora os conjuntos de recursos sejam quase idênticos, essas duas camadas do Sistema de Mensagens do Barramento de Serviço foram desenvolvidas para atender a diferentes casos de uso.

Algumas diferenças de alto nível são destacadas na tabela a seguir.

| Premium | Standard |
| --- | --- |
| Alta taxa de transferência |Taxa de transferência variável |
| Desempenho previsível |Latência variável |
| Preço fixo |Preço pré-pago variável |
| Capacidade de escalar a carga de trabalho verticalmente |N/D |
| Até 1 MB de tamanho de mensagem |Até 256 KB de tamanho de mensagem |

O **Sistema de Mensagens Premium do Barramento de Serviço** fornece isolamento de recursos no nível de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Este contêiner de recursos é chamado de *unidade de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode comprar 1, 2, 4 ou 8 unidades de mensagens para cada espaço de nome Service Bus Premium. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço.

Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido. Service Bus Premium Messaging baseia-se no mecanismo de armazenamento introduzido no [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Com o Sistema de Mensagens Premium, o desempenho de pico é muito mais rápido do que com a camada Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas do sistema de mensagens Premium

As seções a seguir discutem algumas diferenças entre as camadas dos sistemas de mensagens Premium e Standard.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

Não há suporte para filas e tópicos particionados no Sistema de Mensagens Premium. Para saber mais sobre o particionamento, confira as [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Como o sistema de mensagens Premium é executado em um ambiente de tempo de execução totalmente isolado, não há suporte para as entidades expressas em namespaces Premium. Para saber mais sobre o recurso expresso, consulte a propriedade [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se você tiver código em execução no sistema de mensagens padrão e deseja portá-lo para a camada Premium, verifique se a propriedade [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) está definida como **false** (o valor padrão).

## <a name="premium-messaging-resource-usage"></a>Uso de recursos de mensagens premium
Em geral, qualquer operação em uma entidade pode causar o uso de CPU e memória. Aqui estão algumas dessas operações: 

- Operações de gerenciamento como operações CRUD (Create, Retrieve, Update e Delete) em filas, tópicos e assinaturas.
- Operações em tempo de execução (enviar e receber mensagens)
- Monitoramento de operações e alertas

O uso adicional da CPU e da memória não tem preço adicional. Para o nível de Mensagens Premium, há um preço único para a unidade de mensagem.

O uso da CPU e da memória são rastreados e exibidos para você pelas seguintes razões: 

- Fornecer transparência nos internos do sistema
- Entenda a capacidade dos recursos adquiridos.
- Planejamento de capacidade que ajuda você a decidir escalar para cima/para baixo.

## <a name="messaging-unit---how-many-are-needed"></a>Unidade de mensagens - Quantos são necessários?

Ao provisionar um espaço de nome Azure Service Bus Premium, o número de unidades de mensagens alocadas deve ser especificado. Essas unidades de mensagens são recursos dedicados que são alocados no namespace.

O número de unidades de mensagens alocadas no espaço de nome Service Bus Premium pode ser **ajustado dinamicamente** para fator na alteração (aumento ou diminuição) das cargas de trabalho.

Há uma série de fatores a serem considerados ao decidir o número de unidades de mensagens para sua arquitetura:

- Comece com ***1 ou 2 unidades de mensagens*** alocadas no seu namespace.
- Estude as métricas de uso da CPU dentro das [métricas de uso do recurso](service-bus-metrics-azure-monitor.md#resource-usage-metrics) para o seu namespace.
    - Se o uso da CPU estiver ***abaixo de 20%,*** você poderá ***reduzir*** o número de unidades de mensagens alocadas no seu namespace.
    - Se o uso da CPU estiver acima de ***70%,*** seu aplicativo se beneficiará de ***aumentar*** o número de unidades de mensagens alocadas no seu namespace.

O processo de dimensionamento dos recursos alocados em espaços de nome de bus de serviço pode ser automatizado usando [o Azure Automation Runbooks](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> **O dimensionamento** dos recursos alocados no namespace pode ser preventivo ou reativo.
>
>  * **Prevenção**: Se a carga de trabalho adicional for esperada (devido à sazonalidade ou tendências), você pode continuar a alocar mais unidades de mensagens para o namespace antes que as cargas de trabalho sejam atingidas.
>
>  * **Reativo**: Se forem identificadas cargas de trabalho adicionais estudando as métricas de uso de recursos, recursos adicionais podem ser alocados no namespace para incorporar a demanda crescente.
>
> Os medidores de faturamento do Service Bus são de hora em hora. No caso de escalonamento, você só paga os recursos adicionais pelas horas que foram usadas.
>

## <a name="get-started-with-premium-messaging"></a>Introdução ao sistema de mensagens Premium

A introdução ao Sistema de Mensagens Premium é simples e o processo é semelhante ao do Sistema de Mensagens Padrão. Comece pela [criação de um namespace](service-bus-create-namespace-portal.md) no [portal do Azure](https://portal.azure.com). Certifique-se de selecionar **Premium** em **nível de preços**. Clique em **Exibição de detalhes de preço completos** para obter mais informações sobre cada camada.

![criar-premium-namespace][create-premium-namespace]

Você também pode criar um [Namespace Premium usando modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes links:

* [Introdução de Mensagens Premium do Ônibus de Serviço Azure (post no blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Service Bus Messaging](service-bus-messaging-overview.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
