---
title: Segurança de rede para hubs de eventos do Azure
description: Este artigo descreve como configurar o acesso de pontos de extremidade privados
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422776"
---
# <a name="network-security-for-azure-event-hubs"></a>Segurança de rede para hubs de eventos do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com os hubs de eventos do Azure: 

- Marcas de serviço
- Regras de firewall de IP
- Pontos de extremidade de serviço de rede
- Pontos de extremidade privados (versão prévia)


## <a name="service-tags"></a>Marcas de serviço
Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados, minimizando a complexidade das atualizações frequentes para as regras de segurança de rede. Para obter mais informações sobre marcas de serviço, consulte [visão geral das marcas de serviço](../virtual-network/service-tags-overview.md).

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou no [Firewall do Azure](../firewall/service-tags.md). Use marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, **EventHub**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Marca de serviço | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Hubs de eventos do Azure. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces dos hubs de eventos podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais os hubs de eventos do Azure só devem ser acessados de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar hubs de eventos com o [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services), poderá criar uma **regra de firewall** para permitir o tráfego somente de seus endereços IP de infraestrutura local. 

As regras de firewall IP são aplicadas no nível de namespace de hubs de eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [como configurar o firewall IP para um hub de eventos](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos de extremidade de serviço de rede
A integração de Hubs de Eventos com [Pontos de extremidade de serviço de VNet (rede virtual)](../virtual-network/virtual-network-service-endpoints-overview.md) permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceitará mais o tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de Hubs de Eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens. 

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Hubs de Eventos, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público. Há uma exceção a esse comportamento. Habilitar um ponto de extremidade de serviço, por padrão `denyall` , habilita a regra no [Firewall IP](event-hubs-ip-filtering.md) associado à rede virtual. Você pode adicionar endereços IP específicos no firewall de IP para habilitar o acesso ao ponto de extremidade público do hub de eventos. 

> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não há suporte na camada **básica** .

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança rígida e segmentada e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentalizados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação isolados, em que as mensagens são até mesmo gravadas no disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Hubs de Eventos podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Associar hubs de eventos a redes virtuais

As **regras da rede virtual** são o recurso de segurança do firewall que controla se o namespace de Hubs de Eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Hubs de Eventos a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** na sub-rede de uma rede virtual e habilitá-la para **Microsoft. EventHub** , conforme explicado no artigo [visão geral do ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) . Após adicionar o ponto de extremidade de serviço, você associa o namespace de Hubs de Eventos ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Hubs de Eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Hubs de Eventos. Os hubs de eventos em si nunca estabelecem conexões de saída, não precisa obter acesso e, portanto, nunca concedem acesso à sua sub-rede habilitando essa regra.

Para obter mais informações, consulte [como configurar pontos de extremidade de serviço de rede virtual para um hub de eventos](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos de extremidade privados

O [serviço de vínculo privado do Azure](../private-link/private-link-overview.md) permite que você acesse os serviços do Azure (por exemplo, hubs de eventos do Azure, armazenamento do azure e Azure Cosmos DB) e serviços hospedados de cliente/parceiro do Azure em um **ponto de extremidade privado** em sua rede virtual.

Um ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

> [!NOTE]
> Esse recurso tem suporte apenas com a camada **dedicada** . Para obter mais informações sobre a camada dedicada, consulte [visão geral do hubs de eventos dedicados](event-hubs-dedicated-overview.md). 
>
> Este recurso está atualmente em **Visualização**. 


Para obter mais informações, consulte [como configurar pontos de extremidade privados para um hub de eventos](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar o firewall IP para um hub de eventos](event-hubs-ip-filtering.md)
- [Como configurar pontos de extremidade de serviço de rede virtual para um hub de eventos](event-hubs-service-endpoints.md)
- [Como configurar pontos de extremidade privados para um hub de eventos](private-link-service.md)