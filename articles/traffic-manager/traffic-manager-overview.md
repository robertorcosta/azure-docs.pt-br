---
title: Gerenciador de Tráfego do Azure| Microsoft Docs
description: Este artigo fornece uma visão geral do Gerenciador de Tráfego do Azure. Descubra se ele é a escolha certa para balancear a carga do tráfego de usuário para seu aplicativo.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: e2a4db1404709dadb2500df29f3f7acf8787c2b2
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185724"
---
# <a name="what-is-traffic-manager"></a>O que é o Gerenciador de Tráfego?
O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta.

O Gerenciador de Tráfego usa o DNS para direcionar as solicitações do cliente ao ponto de extremidade de serviço mais apropriado com base em um método de roteamento de tráfego e a integridade dos pontos de extremidade. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure. O Gerenciador de Tráfego oferece uma variedade de [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) e [opções de monitoramento de ponto de extremidade](traffic-manager-monitoring.md) para atender às diferentes necessidades dos aplicativos e modelos de failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. Se estiver em busca de informações sobre o encerramento de protocolo TLS ("descarregamento de SSL") ou sobre o processamento de camada de aplicativo por solicitação HTTP/HTTPS, consulte [Gateway de Aplicativo](../application-gateway/overview.md). Caso esteja procurando por balanceamento de carga regional, examine [Load Balancer](../load-balancer/load-balancer-overview.md). Cenários de ponta a ponta podem se beneficiar da combinação dessas soluções conforme for necessário.
>
> Para obter uma comparação das opções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

O Gerenciador de Tráfego oferece os seguintes recursos:

## <a name="increase-application-availability"></a>Aumentar a disponibilidade de aplicativo

O Gerenciador de Tráfego fornece alta disponibilidade para seus aplicativos cruciais ao monitorar seus pontos de extremidade e fornecer failover automático quando um ponto de extremidade fica inativo.
    
## <a name="improve-application-performance"></a>Melhorar o desempenho de aplicativos

O Azure permite executar serviços de nuvem ou sites em data centers espalhados pelo mundo inteiro. O Gerenciador de Tráfego melhora a capacidade de resposta do aplicativo direcionando o tráfego para o ponto de extremidade com a menor latência de rede para o cliente.

## <a name="perform-service-maintenance-without-downtime"></a>Realizar manutenção de serviço sem tempo de inatividade

Você pode executar operações de manutenção planejada em seus aplicativos sem tempo de inatividade. O Gerenciador de Tráfego pode direcionar tráfego para pontos de extremidade alternativos enquanto a manutenção está em andamento.

## <a name="combine-hybrid-applications"></a>Combinar aplicativos híbridos

O Gerenciador de Tráfego tem suporte para pontos de extremidade externos não do Azure habilitando seu uso com implantações locais e de nuvem híbrida, incluindo os cenários de [intermitência para a nuvem](https://azure.microsoft.com/overview/what-is-cloud-bursting/), “migrar para a nuvem” e “failover para a nuvem”.

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuir o tráfego para implantações complexas

Usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md), vários métodos de roteamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para escalar a necessidades de implantações maiores e mais complexas.

## <a name="pricing"></a>Preços

Para obter informações sobre preço, consulte [Preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil do Gerenciador de Tráfego](./quickstart-create-traffic-manager-profile.md).
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-it-works.md).
- Exibir [perguntas frequentes](traffic-manager-FAQs.md) sobre o Gerenciador de Tráfego.