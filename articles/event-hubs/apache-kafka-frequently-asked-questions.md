---
title: Perguntas frequentes-hubs de eventos do Azure para Apache Kafka
description: Este artigo mostra como consumidores e produtores que usam protocolos diferentes (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao usar os Hubs de Eventos do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 066024f657e011aac1449106ec32689f145c5534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314182"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Perguntas frequentes-hubs de eventos para Apache Kafka 
Este artigo fornece respostas para algumas das perguntas frequentes sobre a migração para os hubs de eventos para Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Você executa Apache Kafka?

Não.  Executamos operações de API Kafka na infraestrutura de hubs de eventos.  Porque há uma forte correlação entre Apache Kafka e a funcionalidade AMQP dos hubs de eventos (ou seja, produção, recebimento, gerenciamento etc.), podemos trazer a confiabilidade conhecida dos hubs de eventos para o espaço de PaaS Kafka.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupo de consumidores dos hubs de eventos versus grupo de consumidores Kafka
Qual é a diferença entre um grupo de consumidores de Hub de eventos e um grupo de consumidores Kafka nos hubs de eventos? Os grupos de consumidores Kafka nos hubs de eventos são totalmente distintos dos grupos de consumidores dos hubs de eventos padrão.

**Grupos de consumidores dos hubs de eventos**

- Eles são gerenciados com operações CRUD (criar, recuperar, atualizar e excluir) por meio de modelos de portal, SDK ou Azure Resource Manager. Os grupos de consumidores dos hubs de eventos não podem ser criados.
- Elas são entidades filhas de um hub de eventos. Isso significa que o mesmo nome de grupo de consumidores pode ser reutilizado entre os hubs de eventos no mesmo namespace porque eles são entidades separadas.
- Eles não são usados para armazenar deslocamentos. O consumo de AMQP orquestrado é feito usando o armazenamento de deslocamento externo, por exemplo, o armazenamento do Azure.

**Grupos de consumidores do Kafka**

- Eles são criados.  Os grupos de Kafka podem ser gerenciados por meio das APIs do grupo de consumidores do Kafka.
- Eles podem armazenar deslocamentos no serviço de hubs de eventos.
- Eles são usados como chaves no que é efetivamente um armazenamento de chave-valor de deslocamento. Para um par exclusivo de `group.id` e `topic-partition` , armazenamos um deslocamento no armazenamento do Azure (replicação em 3x). Os usuários dos hubs de eventos não incorrem em custos de armazenamento extra do armazenamento de deslocamentos Kafka. Os deslocamentos são manipulable por meio das APIs do grupo de consumidores do Kafka, mas as *contas* de armazenamento de deslocamento não são diretamente visíveis ou manipulable para usuários do hub de eventos.  
- Eles abrangem um namespace. Usar o mesmo nome de grupo Kafka para vários aplicativos em vários tópicos significa que todos os aplicativos e seus clientes Kafka serão rebalanceados sempre que um único aplicativo precisar de rebalanceamento.  Escolha os nomes de grupo com sabedoria.
- Eles são totalmente distintos dos grupos de consumidores dos hubs de eventos. Você **não** precisa usar ' $default ', nem precisa se preocupar com os clientes Kafka que interferem nas cargas de trabalho do AMQP.
- Eles não são visíveis na portal do Azure. As informações do grupo de consumidores estão acessíveis por meio de APIs do Kafka.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia do desenvolvedor de Apache Kafka para hubs de eventos](apache-kafka-developer-guide.md)
- [Guia de migração de Apache Kafka para hubs de eventos](apache-kafka-migration-guide.md)
- [Guia de solução de problemas Apache Kafka para hubs de eventos](apache-kafka-troubleshooting-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

