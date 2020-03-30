---
title: Visão geral dos hubs de eventos dedicados – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos Hubs de Eventos Azure dedicados, que oferece implantações de um único inquilino de hubs de eventos.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516754"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

*Os clusters event Hubs* oferecem implantações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta de inquilino único tem um SLA garantido de 99,99% e está disponível apenas em nossa camada de preços Dedicados. Um cluster Event Hubs pode insere milhões de eventos por segundo com capacidade garantida e latência sub-segundo. Namespaces e hubs de eventos criados dentro do cluster Dedicated incluem todos os recursos da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui o popular recurso [Event Hubs Capture](event-hubs-capture-overview.md) sem custo adicional, permitindo que você lote e registre automaticamente fluxos de dados para o Azure Storage ou o Azure Data Lake. 

Os clusters são provisionados e cobrados por **CUs (Capacity Units,** unidades de capacidade), uma quantidade pré-alocada de recursos de CPU e memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. O quanto você pode ingerer e transmitir por depende de uma variedade de fatores, como o número de produtores e consumidores, forma de carga, taxa de saída (veja os resultados de benchmark abaixo para mais detalhes). 

> [!NOTE]
> Todos os clusters do Event Hubs são habilitados por kafka por padrão e suportam pontos finais kafka que podem ser usados por seus aplicativos baseados em Kafka existentes. Ter kafka ativado em seu cluster não afeta seus casos de uso não-Kafka; não há opção ou necessidade de desativar Kafka em um cluster.

## <a name="why-dedicated"></a>Por que dedicado?

O Dedicated Event Hubs oferece três benefícios atraentes para clientes que precisam de capacidade em nível corporativo:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>A locação única garante capacidade para um melhor desempenho

Um cluster dedicado garante capacidade em escala total e pode inserir até gigabytes de dados de streaming com armazenamento totalmente durável e latência de sub-segundo para acomodar qualquer explosão no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo aos recursos 
A oferta dedicada inclui recursos como o Capture sem custo adicional, bem como acesso exclusivo a recursos próximos como Bring Your Own Key (BYOK). O serviço também gerencia balanceamento de carga, atualizações de sO, patches de segurança e particionamento para o cliente, para que você possa gastar menos tempo em manutenção de infra-estrutura e mais tempo na construção de recursos do lado do cliente.  

#### <a name="cost-savings"></a>Redução de custos
Em volumes de entrada elevados (>100 TUs), um cluster custa significativamente menos por hora do que comprar uma quantidade comparável de unidades de throughput na oferta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Hubs de eventos cotas e limites dedicados

A oferta Event Hubs Dedicated é cobrada a um preço fixo mensal, com um mínimo de 4 horas de uso. O nível Dedicated oferece todos os recursos do plano Standard, mas com capacidade de escala corporativa e limites para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 por |
| Hubs de Eventos |  10 por namespace | 1000 por namespace |
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Tamanho da mensagem | 1 milhão de bytes | 1 milhão de bytes |
| Partições | 32 por Hub de Eventos | 1024 por Event Hub |
| Grupos de consumidores | 20 por Hub de Eventos | Sem limite por, 1000 por hub de eventos |
| Conexões orientadas | 1.000 incluídos, 5.000 no máximo | 100 K incluído sinuoso e max |
| Retenção de mensagem | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por |
| Capturar | Pagamento por hora | Incluso |

## <a name="how-to-onboard"></a>Como fazer a integração

A experiência de auto-atendimento para [criar um cluster event hubs](event-hubs-dedicated-cluster-create-portal.md) através do Portal [Azure](https://aka.ms/eventhubsclusterquickstart) está agora no Preview. Se você tiver alguma dúvida ou precisar de ajuda para embarcar no Event Hubs Dedicated, entre em contato com a equipe do [Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Perguntas frequentes

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso alcançar com um cluster?

Para um cluster Event Hubs, o quanto você pode ingerr e transmitir depende de vários fatores, como seus produtores, consumidores, a taxa em que você está ingerindo e processando, e muito mais. 

A tabela a seguir mostra os resultados do parâmetro de comparação obtidos durante o teste:

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400k mensagens/seg | 800 MB/s | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | 66,6 mil mensagens/seg | 1,33 GB/s | 133k mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | Mensagens 34k / seg | 1,05 GB/s | Mensagens 34k/seg | 1000 TUs | 250 TUs |

No teste, usamos os critérios a seguir:

- Foi utilizado um cluster de Hubs de Eventos de nível dedicado com quatro unidades de capacidade (UCs). 
- O hub de eventos usado para ingestão tinha 200 partições. 
- Os dados ingeridos foram recebidos por dois aplicativos destinatários que recebem de todas as partições.

#### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar para cima/para baixo o meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de uso. Na versão Preview da experiência de autoatendimento, você pode enviar uma solicitação de [suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para a equipe do Event Hubs *a solicitação de > de cota de > > de cota técnica para escalar ou escalar* para baixo cluster dedicado para escalar seu cluster para cima ou para baixo. Pode levar até 7 dias para concluir a solicitação para reduzir seu cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como a Geo-DR vai funcionar com o meu cluster?

Você pode emparelhar geo-um namespace em um cluster de nível dedicado com outro namespace em um cluster de nível dedicado. Não incentivamos o emparelhamento de um namespace de nível dedicado com um namespace em nossa oferta Standard, uma vez que o limite de throughput será incompatível, o que resultará em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar meus namespaces padrão para pertencer a um cluster de nível dedicado?
No momento, não temos suporte a um processo de migração automatizada para migrar os dados dos hubs de eventos de um namespace padrão para um dedicado. 

## <a name="next-steps"></a>Próximas etapas

Entre em contato com seu representante de vendas da Microsoft ou com o suporte da Microsoft para obter detalhes adicionais sobre o Event Hubs Dedicated. Você também pode criar um cluster ou aprender mais sobre os níveis de preços do Event Hubs visitando os seguintes links:

- [Crie um cluster de Hubs de Eventos através do Portal Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos.
