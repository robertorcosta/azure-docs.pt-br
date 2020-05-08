---
title: Perguntas frequentes-Azure Load Balancer
description: Respostas para perguntas frequentes sobre o Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884482"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="what-types-of-load-balancer-exist"></a>Quais tipos de Load Balancer existem?
Balanceadores de carga internos que equilibram o tráfego em uma VNET e balanceadores de carga externos que balanceam o tráfego de e para um ponto de extremidade conectado à Internet. Para obter mais informações, consulte [Load Balancer types]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types)(. 

Para ambos os tipos, o Azure oferece uma SKU básica e uma SKU standard que têm diferentes recursos funcionais, de desempenho, de segurança e de controle de integridade. Essas diferenças são explicadas em nossa [comparação de SKUhttps://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) ] (artigo.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Como posso atualizar de um básico para um Standard Load Balancer?
Consulte o artigo [Atualizar do básico para o padrão](upgrade-basic-standard.md) para um script automatizado e orientação sobre como atualizar um SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quais são as diferentes opções de balanceamento de carga no Azure?
Consulte o [Guia de tecnologia do balanceador de carga](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) para obter os serviços de balanceamento de carga disponíveis e os usos recomendados para cada um.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Onde posso encontrar Load Balancer modelos de ARM?
Consulte a [lista de modelos de início rápido de Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) para modelos de ARM de implantações comuns.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Como as regras NAT de entrada são diferentes das regras de balanceamento de carga?
As regras de NAT são usadas para especificar um recurso de back-end para rotear o tráfego. Por exemplo, configurar uma porta de balanceador de carga específica para enviar o tráfego de RDP para uma VM específica. As regras de balanceamento de carga são usadas para especificar um pool de recursos de back-end para rotear o tráfego, equilibrando a carga em cada instância. Por exemplo, uma regra de balanceador de carga pode rotear pacotes TCP na porta 80 do balanceador de carga em um pool de servidores Web.

## <a name="next-steps"></a>Próximas etapas
Se sua pergunta não estiver listada acima, envie comentários sobre esta página com sua pergunta. Isso criará um problema do GitHub para a equipe de produto, a fim de garantir que todas as perguntas importantes do cliente sejam respondidas.
