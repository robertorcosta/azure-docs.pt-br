---
title: Camadas Premium e Standard do barramento de serviço do Azure | Microsoft Docs
description: Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço
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
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: cc783dc4b2bf49724f4a2c7ab9cd9904ded2c703
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75352856"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço

O Sistema de Mensagens do Barramento de Serviço, que inclui entidades como filas e tópicos, combina recursos corporativos de mensagens com rica semântica de assinatura para publicação na escala de nuvem. O Sistema de Mensagens do Barramento de Serviço é usado como o backbone de comunicação para muitas soluções de nuvem sofisticadas.

A camada *Premium* do Sistema de Mensagens do Barramento de Serviço atende às solicitações comuns dos clientes em torno da escala, do desempenho e da disponibilidade para aplicativos de missão crítica. A camada Premium é recomendada para cenários de produção. Embora os conjuntos de recursos sejam quase idênticos, essas duas camadas do Sistema de Mensagens do Barramento de Serviço foram desenvolvidas para atender a diferentes casos de uso.

Algumas diferenças de alto nível são destacadas na tabela a seguir.

| Premium | Padrão |
| --- | --- |
| Alto rendimento |Taxa de transferência variável |
| Desempenho previsível |Latência variável |
| Preço fixo |Preço pré-pago variável |
| Capacidade de escalar a carga de trabalho verticalmente |N/D |
| Até 1 MB de tamanho de mensagem |Até 256 KB de tamanho de mensagem |

O **Sistema de Mensagens Premium do Barramento de Serviço** fornece isolamento de recursos no nível de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recurso é chamado de *unidade do sistema de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode comprar 1, 2, 4 ou 8 unidades de mensagens para cada namespace Premium do barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado às. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço.

Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido. O Sistema de Mensagens Premium do Barramento de Serviço se baseia no mecanismo de armazenamento introduzido nos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Com o Sistema de Mensagens Premium, o desempenho de pico é muito mais rápido do que com a camada Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas do sistema de mensagens Premium

As seções a seguir discutem algumas diferenças entre as camadas dos sistemas de mensagens Premium e Standard.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

Não há suporte para filas e tópicos particionados no Sistema de Mensagens Premium. Para saber mais sobre o particionamento, confira as [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Como o sistema de mensagens Premium é executado em um ambiente de tempo de execução totalmente isolado, não há suporte para as entidades expressas em namespaces Premium. Para saber mais sobre o recurso expresso, consulte a propriedade [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se você tiver código em execução no sistema de mensagens padrão e deseja portá-lo para a camada Premium, verifique se a propriedade [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) está definida como **false** (o valor padrão).

## <a name="premium-messaging-resource-usage"></a>Uso de recursos do sistema de mensagens Premium
Em geral, qualquer operação em uma entidade pode causar o uso de CPU e memória. Aqui estão algumas dessas operações: 

- Operações de gerenciamento, como CRUD (criar, recuperar, atualizar e excluir) em filas, tópicos e assinaturas.
- Operações de tempo de execução (enviar e receber mensagens)
- Monitoramento de operações e alertas

No entanto, o uso adicional de CPU e memória não tem preços. Para a camada de mensagens Premium, há um único preço para a unidade de mensagem.

O uso de CPU e memória é acompanhado e exibido para você pelos seguintes motivos: 

- Fornecer transparência para os elementos internos do sistema
- Entenda a capacidade dos recursos comprados.
- Planejamento de capacidade que ajuda você a decidir Expandir/reduzir.

## <a name="messaging-unit---how-many-are-needed"></a>Unidade de mensagens – quantas são necessárias?

Ao provisionar um namespace Premium do barramento de serviço do Azure, o número de unidades de mensagens alocadas deve ser especificado. Essas unidades de mensagens são recursos dedicados alocados para o namespace.

O número de unidades de mensagens alocadas para o namespace Premium do barramento de serviço pode ser **ajustado dinamicamente** para fator na alteração (aumentar ou diminuir) em cargas de trabalho.

Há vários fatores a serem levados em consideração ao decidir o número de unidades de mensagens para sua arquitetura:

- Comece com ***1 ou 2 unidades de mensagens*** alocadas para seu namespace.
- Estudar as métricas de uso da CPU dentro das [métricas de uso de recursos](service-bus-metrics-azure-monitor.md#resource-usage-metrics) para seu namespace.
    - Se o uso da CPU estiver ***abaixo de 20%***, você poderá ***reduzir verticalmente*** o número de unidades de mensagens alocadas para o namespace.
    - Se o uso da CPU estiver ***acima de 70%***, seu aplicativo se beneficiará do ***aumento*** do número de unidades de mensagens alocadas para o namespace.

O processo de dimensionamento dos recursos alocados para um namespace do barramento de serviço pode ser automatizado usando [Runbooks de automação do Azure](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> O **dimensionamento** dos recursos alocados para o namespace pode ser preemptivo ou reativo.
>
>  * **Preemptive**: se uma carga de trabalho adicional for esperada (devido a sazonalidade ou tendências), você poderá continuar alocando mais unidades de mensagens ao namespace antes de atingir as cargas de trabalho.
>
>  * **Reativo**: se cargas de trabalho adicionais forem identificadas ao estudar as métricas de uso de recursos, os recursos adicionais poderão ser alocados para o namespace para incorporar a demanda crescente.
>
> Os medidores de cobrança para o barramento de serviço são por hora. No caso de expansão, você paga apenas pelos recursos adicionais para as horas em que eles foram usados.
>

## <a name="get-started-with-premium-messaging"></a>Introdução ao sistema de mensagens Premium

A introdução ao Sistema de Mensagens Premium é simples e o processo é semelhante ao do Sistema de Mensagens Padrão. Comece pela [criação de um namespace](service-bus-create-namespace-portal.md) no [portal do Azure](https://portal.azure.com). Verifique se você selecionou **Premium** em **Tipo de preços**. Clique em **Exibição de detalhes de preço completos** para obter mais informações sobre cada camada.

![criar-premium-namespace][create-premium-namespace]

Você também pode criar um [Namespace Premium usando modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes links:

* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (postagem de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Sistema de Mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
