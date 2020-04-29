---
title: Segurança de rede para o barramento de serviço do Azure
description: Este artigo descreve os recursos de segurança de rede, como marcas de serviço, regras de firewall de IP, pontos de extremidade de serviço e pontos de extremidade privados.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479274"
---
# <a name="network-security-for-azure-service-bus"></a>Segurança de rede para o barramento de serviço do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com o barramento de serviço do Azure: 

- Marcas de serviço
- Regras de firewall de IP
- Pontos de extremidade de serviço de rede
- Pontos de extremidade privados (versão prévia)


## <a name="service-tags"></a>Marcas de serviço
Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados, minimizando a complexidade das atualizações frequentes para as regras de segurança de rede. Para obter mais informações sobre marcas de serviço, consulte [visão geral das marcas de serviço](../virtual-network/service-tags-overview.md).

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/security-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). Use marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, **ServiceBus**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Marca de serviço | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | O tráfego do barramento de serviço do Azure que usa a camada de serviço Premium. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces do barramento de serviço podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais o barramento de serviço do Azure deve ser acessível somente de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar o barramento de serviço com [rota expressa do Azure] [rota expressa], poderá criar uma **regra de firewall** para permitir o tráfego somente de endereços IP de infraestrutura local ou endereços de um gateway de NAT corporativo. 

As regras de firewall IP são aplicadas no nível de namespace do barramento de serviço. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [como configurar o firewall IP para um namespace do barramento de serviço](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos de extremidade de serviço de rede
A integração do barramento de serviço com [pontos de extremidade de serviço de rede virtual (VNet)](service-bus-service-endpoints.md) permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Após ser configurado para associar-se a pelo menos um ponto de extremidade de serviço de sub-rede da rede virtual, o respectivo namespace de Barramento de Serviço não aceitará mais tráfego de nenhum lugar, exceto das redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

> [!IMPORTANT]
> As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md).
> 
> Ao usar pontos de extremidade de serviço de VNet com o barramento de serviço, você não deve habilitar esses pontos de extremidade em aplicativos que misturam namespaces de barramento de serviço de camada Standard e Premium. Porque a camada Standard não oferece suporte a VNets. O ponto de extremidade é restrito somente a namespaces da camada Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Associar o barramento de serviço a redes virtuais

As *regras da rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-lo para **Microsoft. ServiceBus** , conforme explicado na [visão geral do ponto de extremidade de serviço](service-bus-service-endpoints.md). Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

Para obter mais informações, consulte [como configurar pontos de extremidade de serviço de rede virtual para um namespace do barramento de serviço](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos de extremidade privados

O serviço de vínculo privado do Azure permite que você acesse os serviços do Azure (por exemplo, barramento de serviço do Azure, armazenamento do Azure e Azure Cosmos DB) e serviços hospedados de cliente/parceiro do Azure em um **ponto de extremidade privado** em sua rede virtual.

Um ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Esse recurso é compatível com a camada **Premium** do barramento de serviço do Azure. Para obter mais informações sobre a camada Premium, consulte o artigo sobre as [camadas de sistema de mensagens Premium e Standard do barramento de serviço](service-bus-premium-messaging.md) .
>
> Este recurso está atualmente em **Visualização**. 


Para obter mais informações, consulte [como configurar pontos de extremidade privados para um namespace do barramento de serviço](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar o firewall IP para um namespace do barramento de serviço](service-bus-ip-filtering.md)
- [Como configurar pontos de extremidade de serviço de rede virtual para um namespace do barramento de serviço](service-bus-service-endpoints.md)
- [Como configurar pontos de extremidade privados para um namespace do barramento de serviço](private-link-service.md)
