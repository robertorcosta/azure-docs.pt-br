---
title: Segurança de rede para hubs de eventos do Azure
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422776"
---
# <a name="network-security-for-azure-event-hubs"></a>Segurança de rede para hubs de eventos do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com o Azure Event Hubs: 

- Marcas de serviço
- Regras do Firewall IP
- Pontos finais do serviço de rede
- Pontos finais privados (visualização)


## <a name="service-tags"></a>Marcas de serviço
Uma tag de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre tags de serviço, consulte [visão geral das tags de serviço](../virtual-network/service-tags-overview.md).

Você pode usar tags de serviço para definir controles de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou [Firewall Azure](../firewall/service-tags.md). Use tags de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, **EventHub**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Tag de serviço | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall Do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Hubs de Eventos Azure. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces do Event Hubs são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esse recurso é útil em cenários nos quais o Azure Event Hubs só deve ser acessível a partir de certos sites conhecidos. As regras do firewall permitem configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se você usar hubs de eventos com [o Azure Express Route,](/azure/expressroute/expressroute-faqs#supported-services)você poderá criar uma regra de **firewall** para permitir o tráfego apenas de seus endereços IP de infra-estrutura no local. 

As regras de firewall IP são aplicadas no nível de namespace do Event Hubs. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [Como configurar firewall IP para um hub de eventos](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos finais do serviço de rede
A integração de Hubs de Eventos com [Pontos de extremidade de serviço de VNet (rede virtual)](../virtual-network/virtual-network-service-endpoints-overview.md) permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez configurado para limitar-se a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respectivo namespace Event Hubs não aceita mais tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de Hubs de Eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens. 

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Hubs de Eventos, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público. Há uma exceção para esse comportamento. Habilitar um ponto final de serviço, por padrão, permite que a `denyall` regra no firewall [IP](event-hubs-ip-filtering.md) associado à rede virtual. Você pode adicionar endereços IP específicos no firewall IP para habilitar o acesso ao ponto final público do Event Hub. 

> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não é suportado no nível **básico.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que requerem segurança apertada e compartimentada, e onde as subredes de rede virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são escritas em disco à medida que transitam entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Hubs de Eventos podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Vincular hubs de eventos a redes virtuais

As **regras da rede virtual** são o recurso de segurança do firewall que controla se o namespace de Hubs de Eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Hubs de Eventos a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um ponto final de **serviço de rede virtual** na sub-rede de uma rede virtual e habilitá-lo para o **Microsoft.EventHub,** conforme explicado no artigo de visão geral do [ponto final do serviço.](../virtual-network/virtual-network-service-endpoints-overview.md) Após adicionar o ponto de extremidade de serviço, você associa o namespace de Hubs de Eventos ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Hubs de Eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Hubs de Eventos. O Event Hubs em si nunca estabelece conexões de saída, não precisa ter acesso e, portanto, nunca é concedido acesso à sua sub-rede, permitindo essa regra.

Para obter mais informações, consulte [Como configurar pontos finais de serviço de rede virtual para um hub de eventos](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos finais privados

[O serviço Azure Private Link](../private-link/private-link-overview.md) permite que você acesse os Serviços Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure hospedou serviços de clientes/parceiros em um **ponto final privado** em sua rede virtual.

Um ponto final privado é uma interface de rede que conecta você de forma privada e segura a um serviço alimentado pelo Azure Private Link. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

> [!NOTE]
> Esse recurso é suportado apenas com o nível **dedicado.** Para obter mais informações sobre o nível dedicado, consulte [Visão geral do Event Hubs Dedicated](event-hubs-dedicated-overview.md). 
>
> Este recurso está atualmente em **pré-visualização**. 


Para obter mais informações, consulte [Como configurar pontos finais privados para um hub de eventos](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar firewall IP para um hub de eventos](event-hubs-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um hub de eventos](event-hubs-service-endpoints.md)
- [Como configurar pontos finais privados para um hub de eventos](private-link-service.md)