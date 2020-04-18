---
title: Perguntas frequentes - Azure Event Hubs para Apache Kafka
description: Este artigo mostra como consumidores e produtores que usam protocolos diferentes (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao usar os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606737"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Perguntas frequentes - Hubs de eventos para Apache Kafka 
Este artigo fornece respostas para algumas das perguntas freqüentes sobre a migração para Hubs de Eventos para Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Você dirige Apache Kafka?

Não.  Executamos operações de API kafka contra infra-estrutura event hubs.  Como há uma correlação estreita entre a funcionalidade AmQP apache e event hubs (ou seja, produzir, receber, gerenciamento, assim por diante.), somos capazes de trazer a confiabilidade conhecida dos Event Hubs para o espaço Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupo de consumidores Event Hubs vs. Grupo de consumidores Kafka
Qual é a diferença entre um grupo de consumidores do Event Hub e um grupo de consumidores Kafka no Event Hubs? Os grupos de consumidores kafka em Event Hubs são totalmente distintos dos grupos de consumidores padrão do Event Hubs.

**Grupos de consumidores do Event Hubs**

- Eles são gerenciados com as operações de criação, recuperação, atualização e exclusão (CRUD) através de modelos de portal, SDK ou Azure Resource Manager. Os grupos de consumidores do Event Hubs não podem ser criados automaticamente.
- São entidades infantis de um centro de eventos. Isso significa que o mesmo nome do grupo de consumidores pode ser reutilizado entre hubs de eventos no mesmo namespace porque são entidades separadas.
- Eles não são usados para armazenar compensações. O consumo orquestrado de AMQP é feito usando armazenamento de deslocamento externo, por exemplo, o Armazenamento Azure.

**Grupos de consumidores kafka**

- Eles são auto-criados.  Os grupos Kafka podem ser gerenciados através das APIs do grupo de consumidores Kafka.
- Eles podem armazenar compensações no serviço Event Hubs.
- Eles são usados como chaves no que é efetivamente uma loja de valor-chave offset. Para um par `group.id` `topic-partition`único de e , armazenamos um offset no Azure Storage (replicação de 3x). Os usuários do Event Hubs não incorrem em custos extras de armazenamento ao armazenar deslocamentos kafka. As compensações são manipuláveis através das APIs do grupo de consumidores Kafka, mas as *contas* de armazenamento offset não são diretamente visíveis ou manipuláveis para usuários do Event Hub.  
- Eles abrangem um espaço de nome. Usar o mesmo nome do grupo Kafka para vários aplicativos em vários tópicos significa que todos os aplicativos e seus clientes Kafka serão reequilibrados sempre que apenas um único aplicativo precisar de reequilíbrio.  Escolha os nomes do seu grupo sabiamente.
- Eles são totalmente distintos dos grupos de consumidores do Event Hubs. Você **não** precisa usar '$Default', nem precisa se preocupar com clientes Kafka interferindo nas cargas de trabalho AMQP.
- Eles não podem ser visualizados no portal Azure. As informações do grupo de consumidores são acessíveis através de APIs kafka.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia de desenvolvedores Apache Kafka para Hubs de Eventos](apache-kafka-developer-guide.md)
- [Guia de migração Apache Kafka para Hubs de Eventos](apache-kafka-migration-guide.md)
- [Guia de solução de problemas apache Kafka para Hubs de Eventos](apache-kafka-troubleshooting-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

