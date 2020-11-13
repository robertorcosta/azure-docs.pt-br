---
title: Perguntas frequentes padrão sobre a proteção contra DDoS do Azure
description: Perguntas frequentes sobre o padrão de proteção contra DDoS do Azure, que ajuda a fornecer defesa contra ataques de DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: ed524642dc53835e686f52b53cfce0c16fb56377
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579089"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Perguntas frequentes padrão sobre a proteção contra DDoS do Azure

Este artigo responde a perguntas comuns sobre a proteção contra DDoS do Azure Standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>O que é um ataque de DDoS (negação de serviço distribuído)?
A negação de serviço distribuída, ou DDoS, é um tipo de ataque em que um invasor envia mais solicitações a um aplicativo do que o aplicativo é capaz de lidar. O efeito resultante é que os recursos estão sendo esgotados, afetando a disponibilidade do aplicativo e a capacidade de atender a seus clientes. Nos últimos anos, o setor observou um aumento de ataques, com ataques se tornando mais sofisticados e maiores em magnitude. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>O que é o serviço padrão de proteção contra DDoS do Azure?
A proteção contra DDoS do Azure Standard, combinada com as práticas recomendadas de design de aplicativos, fornece recursos aprimorados de mitigação de DDoS para se defender contra ataques de DDoS. Ele é ajustado automaticamente para ajudar a proteger seus recursos específicos do Azure em uma rede virtual. É muito simples habilitar a proteção em qualquer rede virtual nova ou existente, e ela não exige nenhum aplicativo ou alterações de recursos. Ela tem várias vantagens em comparação com o serviço básico, incluindo registro em log, alertas e telemetria. Consulte [visão geral da proteção contra DDoS do Azure Standard](ddos-protection-overview.md). 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>E quanto à proteção na camada de serviço (camada 7)?
Os clientes podem usar o serviço de proteção contra DDoS do Azure em combinação com a [SKU do WAF do gateway de aplicativo](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) para proteção na camada de rede (camada 3 e 4, oferecida pelo serviço de proteção contra DDoS do Azure) e na camada de aplicativo (camada 7, oferecida pela SKU do WAF do gateway de aplicativo).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Os serviços não são seguros no Azure sem o serviço?
Os serviços em execução no Azure são protegidos inerentemente pela proteção contra DDoS do Azure básica que está em vigor para proteger a infraestrutura do Azure. No entanto, a proteção que protege a infraestrutura tem um limite muito maior do que a maioria dos aplicativos tem a capacidade de lidar e não fornece telemetria ou alertas, portanto, embora um volume de tráfego possa ser percebido como inofensivo pela plataforma, ele pode ser devastador para o aplicativo que o recebe. 

Ao realizar a integração com o serviço Standard de proteção contra DDoS do Azure, o aplicativo obtém monitoramento dedicado para detectar ataques e limites específicos do aplicativo. Um serviço será protegido com um perfil que é ajustado para seu volume de tráfego esperado, fornecendo uma defesa muito mais rígida contra ataques de DDoS.

## <a name="what-are-the-supported-protected-resource-types"></a>Quais são os tipos de recursos protegidos com suporte?
Os IPs públicos no ARM com base em VNETs são atualmente o único tipo de recurso protegido. Os serviços de PaaS (multilocatário) não têm suporte em apresentado. Consulte [arquiteturas de referência padrão da proteção contra DDoS do Azure](ddos-protection-reference-architectures.md).

## <a name="are-classicrdfe-protected-resources-supported"></a>Há suporte para recursos protegidos clássicos/RDFE?
Somente recursos protegidos baseados em ARM têm suporte na visualização. Não há suporte para VMs em implantações clássicas/RDFE. O suporte não está atualmente planejado para recursos clássicos/RDFE. Consulte [arquiteturas de referência padrão da proteção contra DDoS do Azure](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Posso proteger meus recursos locais usando a proteção contra DDoS?
Você precisa ter os pontos de extremidade públicos do seu serviço associado a uma VNet no Azure para serem habilitados para proteção contra DDoS. Os designs de exemplo incluem:
- Sites da Web (IaaS) em bancos de dados do Azure e back-end no datacenter local. 
- Gateway de aplicativo no Azure (proteção contra DDoS habilitada no gateway de aplicativo/WAF) e sites em data centers locais.

Consulte [arquiteturas de referência padrão da proteção contra DDoS do Azure](ddos-protection-reference-architectures.md).

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Posso registrar um domínio fora do Azure e associá-lo a um recurso protegido, como VM ou ELB?
Para os cenários de IP público, o serviço de proteção contra DDoS dará suporte a qualquer aplicativo, independentemente de onde o domínio associado é registrado ou hospedado, desde que o IP público associado esteja hospedado no Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Posso configurar manualmente a política de DDoS aplicada aos IPs VNets/públicos?
Não, a personalização de política não está disponível neste momento.

## <a name="can-i-allowlistbloclist-specific-ip-addresses"></a>Posso permitir endereços IP específicos deList/bloclist?
Não, a configuração manual não está disponível neste momento.

## <a name="how-can-i-test-ddos-protection"></a>Como posso testar a proteção contra DDoS?
Consulte [testando por meio de simulações](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Quanto tempo leva para que as métricas sejam carregadas no portal?
As métricas devem ser visíveis no portal dentro de 5 minutos. Se o recurso estiver sob ataque, outras métricas começarão a aparecer no portal dentro de 5-7 minutos. 
    



