---
title: Segurança de rede para ônibus de serviço Azure
description: Este artigo descreve recursos de segurança de rede, como tags de serviço, regras de firewall IP, pontos finais de serviço e pontos finais privados.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479274"
---
# <a name="network-security-for-azure-service-bus"></a>Segurança de rede para ônibus de serviço Azure 
Este artigo descreve como usar os seguintes recursos de segurança com o Azure Service Bus: 

- Marcas de serviço
- Regras do Firewall IP
- Pontos finais do serviço de rede
- Pontos finais privados (visualização)


## <a name="service-tags"></a>Marcas de serviço
Uma tag de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre tags de serviço, consulte [visão geral das tags de serviço](../virtual-network/service-tags-overview.md).

Você pode usar tags de serviço para definir controles de acesso à rede em [grupos de segurança](../virtual-network/security-overview.md#security-rules) de rede ou Firewall [Azure](../firewall/service-tags.md). Use tags de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, **ServiceBus**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Tag de serviço | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall Do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Servicebus** | Tráfego de ônibus de serviço do Azure que usa o nível de serviço Premium. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces do Service Bus são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esse recurso é útil em cenários nos quais o Azure Service Bus só deve ser acessível a partir de certos sites conhecidos. As regras do firewall permitem configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se você usar o Service Bus com [Azure Express Route][rota expressa], você pode criar uma regra de **firewall** para permitir o tráfego apenas de seus endereços IP de infra-estrutura no local ou endereços de um gateway NAT corporativo. 

As regras de firewall IP são aplicadas no nível de namespace do Service Bus. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [Como configurar o firewall IP para um](service-bus-ip-filtering.md) espaço de nome de Ônibus de Serviço

## <a name="network-service-endpoints"></a>Pontos finais do serviço de rede
A integração dos pontos finais de serviço service bus com [virtual network (VNet)](service-bus-service-endpoints.md) permite acesso seguro aos recursos de mensagens a partir de cargas de trabalho como máquinas virtuais vinculadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Após ser configurado para associar-se a pelo menos um ponto de extremidade de serviço de sub-rede da rede virtual, o respectivo namespace de Barramento de Serviço não aceitará mais tráfego de nenhum lugar, exceto das redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

> [!IMPORTANT]
> As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md).
> 
> Ao usar os pontos finais de serviço da VNet com o Service Bus, você não deve habilitar esses pontos finais em aplicativos que misturam os namespaces de barramento de serviço padrão e premium. Porque o nível Standard não suporta VNets. O ponto final é restrito apenas aos namespaces de nível Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Vincular ônibus de serviço a redes virtuais

As *regras da rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto final de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-lo para **microsoft.ServiceBus,** conforme explicado na visão geral do [ponto final do serviço](service-bus-service-endpoints.md). Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

Para obter mais informações, consulte [Como configurar pontos finais de serviço de rede virtual para um espaço de nome de Ônibus de Serviço](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos finais privados

O Azure Private Link Service permite que você acesse os serviços do Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e o Azure hospedou serviços de clientes/parceiros em um **ponto final privado** em sua rede virtual.

Um ponto final privado é uma interface de rede que conecta você de forma privada e segura a um serviço alimentado pelo Azure Private Link. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, consulte [O que é o Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Este recurso é suportado com o nível **premium** do Azure Service Bus. Para obter mais informações sobre o nível premium, consulte o artigo [de camadas de mensagens Service Bus Premium e Standard.](service-bus-premium-messaging.md)
>
> Este recurso está atualmente em **pré-visualização**. 


Para obter mais informações, consulte [Como configurar pontos finais privados para um espaço de nome de Ônibus de Serviço](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar firewall IP para um espaço de nome de Ônibus de Serviço](service-bus-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um espaço de nome de Ônibus de Serviço](service-bus-service-endpoints.md)
- [Como configurar pontos finais privados para um espaço de nome de Ônibus de Serviço](private-link-service.md)
