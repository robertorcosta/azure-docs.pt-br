---
title: Visão geral dos hubs de eventos dedicados – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos hubs de eventos dedicados do Azure, que oferece implantações de um único locatário de hubs de eventos.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 70061b5dc4fe72c9fd2fd60dd8c67da31b1d1e6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322442"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

Os *clusters de hubs de eventos* oferecem implantações de locatário único para clientes com as necessidades de streaming mais exigentes. Essa oferta de locatário único tem um SLA de 99,99% garantido e está disponível apenas em nosso tipo de preço dedicado. Um cluster de hubs de eventos pode ingressar em milhões de eventos por segundo com a capacidade garantida e a latência de menos de segundo. Os namespaces e os hubs de eventos criados no cluster dedicado incluem todos os recursos da oferta padrão e muito mais, mas sem nenhum limite de entrada. Ele também inclui o recurso popular de [captura de hubs de eventos](event-hubs-capture-overview.md) sem custo adicional, permitindo que você faça automaticamente o lote e o log de fluxos de dados para o armazenamento do Azure ou Azure data Lake. 

Os clusters são provisionados e cobrados por **unidades de capacidade (cus)**, uma quantidade alocada de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. O quanto você pode ingerir e transmitir por CU depende de uma variedade de fatores, como o número de produtores e consumidores, a forma de carga, a taxa de egresso (consulte os resultados de parâmetro de comparação abaixo para obter mais detalhes). 

> [!NOTE]
> Todos os clusters de hubs de eventos são habilitados para Kafka por padrão e dão suporte a pontos de extremidade Kafka que podem ser usados por seus aplicativos baseados em Kafka existentes. Ter o Kafka habilitado no cluster não afeta os casos de uso não Kafka; Não há nenhuma opção ou é necessário desabilitar o Kafka em um cluster.

## <a name="why-dedicated"></a>Por que o dedicado?

Os hubs de eventos dedicados oferecem três benefícios atraentes para clientes que precisam de capacidade de nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>A locação única garante capacidade para melhor desempenho

Um cluster dedicado garante a capacidade em escala total e pode fazer a entrada de até gigabytes de dados de streaming com o armazenamento totalmente durável e a latência de menos de segundo para acomodar qualquer intermitência no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo aos recursos 
A oferta dedicada inclui recursos como a captura sem custo adicional, bem como acesso exclusivo a recursos futuros como o Bring Your Own Key (BYOK). O serviço também gerencia o balanceamento de carga, as atualizações do sistema operacional, OS patches de segurança e o particionamento para o cliente, para que você possa gastar menos tempo na manutenção da infraestrutura e mais tempo na criação de recursos do lado do cliente.  

#### <a name="cost-savings"></a>Economia de custos
Em volumes de entrada altos (>100 TUs), um cluster custa significativamente menos por hora do que comprar uma quantidade comparável de unidades de produtividade na oferta standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Cotas e limites de Hubs de Eventos Dedicados

A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de quatro horas de uso. O nível Dedicado oferece todos os recursos do plano Standard, mas com capacidade em escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 por CU |
| Hubs de Eventos |  10 por namespace | 1\.000 por namespace |
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Tamanho da mensagem | 1 milhão bytes | 1 milhão bytes |
| Partições | 32 por Hub de eventos | 1024 por Hub de eventos |
| Grupos de consumidores | 20 por Hub de eventos | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 1.000 incluso, 5.000 máx. | 100 K incluídos e máximo |
| Retenção de mensagem | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por CU |
| Capturar | Pagamento por hora | Incluso |

## <a name="how-to-onboard"></a>Como fazer a integração

A experiência de autoatendimento para [criar um cluster de hubs de eventos](event-hubs-dedicated-cluster-create-portal.md) por meio do [portal do Azure](https://aka.ms/eventhubsclusterquickstart) agora está em versão prévia. Se você tiver alguma dúvida ou precisar de integração de ajuda para Hubs de Eventos Dedicados, entre em contato com a [equipe dos hubs de eventos](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Perguntas frequentes

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um cluster?

Para um cluster de hubs de eventos, quanto você pode ingerir e transmitir depende de vários fatores, como seus produtores, consumidores, a taxa na qual você está ingerindo e processando e muito mais. 

A tabela a seguir mostra os resultados do parâmetro de comparação obtidos durante o teste:

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400 mil mensagens/s | 800 MB/s | 800 mil mensagens/s | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | 66,6 mil mensagens/s | 1,33 GB/s | 133 mil mensagens/s | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | 34 mil mensagens/s | 1,05 GB/s | 34 mil mensagens/s | 1000 TUs | 250 TUs |

No teste, usamos os critérios a seguir:

- Um cluster de hubs de eventos de camada dedicada com quatro unidades de capacidade (CUs) foi usado. 
- O hub de eventos usado para ingestão tinha 200 partições. 
- Os dados ingeridos foram recebidos por dois aplicativos destinatários que recebem de todas as partições.

#### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar/reduzir verticalmente o meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de uso. Na versão de visualização da experiência de autoatendimento, você pode enviar uma [solicitação de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para a equipe de hubs de eventos sob a solicitação de *> de cota > para escalar ou reduzir verticalmente o cluster dedicado* para dimensionar ou reduzir verticalmente o cluster. Pode levar até 7 dias para concluir a solicitação para reduzir verticalmente o cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como o geo-DR funcionará com o meu cluster?

Você pode emparelhar geograficamente um namespace em um cluster de camada dedicada com outro namespace em um cluster de camada dedicada. Não incentivamos o emparelhamento de um namespace de camada dedicada com um namespace em nossa oferta padrão, uma vez que o limite de taxa de transferência será incompatível, o que resultará em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar meus namespaces padrão para pertencer a um cluster de camada dedicada?
No momento, não damos suporte a um processo de migração automatizado para migrar seus dados de hubs de eventos de um namespace padrão para um dedicado. 

## <a name="next-steps"></a>Próximas etapas

Entre em contato com seu representante de vendas da Microsoft ou Suporte da Microsoft para obter detalhes adicionais sobre Hubs de Eventos Dedicados. Você também pode criar um cluster ou saber mais sobre os tipos de preço dos hubs de eventos visitando os links a seguir:

- [Criar um cluster de hubs de eventos por meio do portal do Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos.
