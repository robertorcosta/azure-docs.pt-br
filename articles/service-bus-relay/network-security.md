---
title: Segurança de rede para retransmissão do Azure
description: Este artigo descreve como configurar o acesso de pontos de extremidade privados
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984766"
---
# <a name="network-security-for-azure-relay"></a>Segurança de rede para retransmissão do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com a retransmissão do Azure: 

- Regras de firewall de IP (versão prévia)
- Pontos de extremidade privados (versão prévia)

> [!NOTE]
> A retransmissão do Azure não dá suporte a pontos de extremidade de serviço de rede. 


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces de retransmissão são acessíveis da Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais a retransmissão do Azure deve ser acessada apenas de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar a retransmissão com o [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services), poderá criar uma **regra de firewall** para permitir o tráfego somente de seus endereços IP de infraestrutura local. 

As regras de firewall IP são aplicadas no nível do namespace de retransmissão. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace de retransmissão é rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [como configurar o firewall IP para um namespace de retransmissão](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Pontos de extremidade privados

O **serviço de vínculo privado** do Azure permite que você acesse os serviços do Azure (por exemplo, retransmissão do Azure, barramento de serviço do Azure, hubs de eventos do Azure, armazenamento do azure e Azure Cosmos DB) e serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. Para obter mais informações, confira [O que é o Link Privado do Azure (versão prévia)?](../private-link/private-link-overview.md)

Um **ponto de extremidade privado** é uma interface de rede que permite que suas cargas de trabalho em execução em uma rede virtual se conectem de forma privada e segura a um serviço que tem um **recurso de link privado** (por exemplo, um namespace de retransmissão). O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado, portanto, não são necessários gateways, dispositivos NAT, ExpressRoute, conexões VPN ou endereços IP públicos. O tráfego entre sua rede virtual e o serviço atravessa a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode fornecer um nível de granularidade no controle de acesso, permitindo conexões a namespaces específicos de retransmissão do Azure.

> [!NOTE]
> Este recurso está atualmente em **Visualização**. 

Para obter mais informações, consulte [como configurar pontos de extremidade privados](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar o firewall IP](ip-firewall-virtual-networks.md)
- [Como configurar pontos de extremidade privados](private-link-service.md)