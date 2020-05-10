---
title: SD-arquitetura de conectividade de WAN
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre a interconexão de um SD-WAN privado com a WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 15e44b9c048f167935fe8660228581e5bac0f43d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006255"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Arquitetura de conectividade SD-WAN com a WAN virtual do Azure

A WAN virtual do Azure é um serviço de rede que reúne muitos serviços de segurança e conectividade em nuvem com uma única interface operacional. Esses serviços incluem ramificação (via VPN site a site), usuário remoto (VPN ponto a site), conectividade privada (ExpressRoute), bem como conectividade transitiva entre nuvem para Vnets, VPN e ExpressRoute interconectividade, roteamento, firewall do Azure e criptografia para conectividade privada.

Embora a própria WAN virtual do Azure seja uma WAN definida pelo software (SD-WAN), ela também foi projetada para permitir a interconexão direta com as tecnologias e os serviços do SD-WAN baseados no local. Muitos desses serviços são oferecidos por nosso ecossistema [virtual Wan](virtual-wan-locations-partners.md) e parceiros de serviços gerenciados de rede do Azure [(MSPs)](../networking/networking-partners-msp.md). As empresas que estão transformando sua WAN privada para SD-WAN têm opções ao interconectar seu SD-WAN privado com a WAN virtual do Azure. As empresas podem escolher entre essas opções:

* Modelo de interconexão direta
* Modelo de interconexão indireta
* Modelo de WAN híbrido gerenciado usando seu provedor de serviço gerenciado favorito [MSP](../networking/networking-partners-msp.md)

Em todos esses casos, a interconexão de WAN virtual com SD-WAN é semelhante do lado da conectividade, mas pode variar na orquestração e no lado operacional.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modelo de interconexão direta

![Modelo de interconexão direta](./media/sd-wan-connectivity-architecture/direct.png)

Nesse modelo de arquitetura, o CPE (equipamento local do cliente) da Branch WAN é conectado diretamente a hubs de WAN virtuais por meio de conexões IPsec. A CPE da ramificação também pode estar conectada a outras ramificações por meio do SD-WAN privado ou aproveitar a WAN virtual para conectividade de Branch para Branch. As ramificações que precisam acessar suas cargas de trabalho no Azure poderão acessar de forma direta e segura o Azure por meio de túnel (s) IPsec que são encerrados nos hubs de WAN virtuais.

Os parceiros do SD-WAN CPE podem habilitar a automação para automatizar a conectividade IPsec normalmente entediante e propenso a erros de seus respectivos dispositivos CPE. A automação permite que o controlador SD-WAN se comunique com o Azure por meio da API WAN virtual para configurar os sites de WAN virtual, bem como enviar por push a configuração de túnel IPsec necessária para o Branch CPEs. Consulte [diretrizes de automação](virtual-wan-configure-automation-providers.md) para obter a descrição da automação de interconexão de WAN virtual por vários parceiros SD-Wan.

O SD-WAN CPE continua sendo o local onde a otimização de tráfego, bem como a seleção de caminho, é implementada e imposta. 

Nesse modelo, uma otimização de tráfego proprietário do fornecedor com base em características de tráfego em tempo real pode não ter suporte, pois a conectividade com a WAN virtual é sobre IPsec e a VPN IPsec é encerrada no gateway de VPN de WAN virtual. Por exemplo, a seleção de caminho dinâmico na ramificação CPE é viável porque o dispositivo de ramificação está trocando várias informações de pacote de rede com outro nó SD-WAN, portanto, identificando o melhor link a ser usado para vários tráfegos priorizados dinamicamente na ramificação. Esse recurso pode ser útil em áreas em que a otimização da última quilometragem (Branch para o POP da Microsoft mais próximo) é necessária.

Com a WAN virtual, os usuários podem obter a seleção de caminho do Azure, que é a seleção de caminho baseada em políticas entre vários links de ISP da ramificação de CPE para gateways VPN de WAN virtual. A WAN virtual permite a configuração de vários links (caminhos) para o mesmo SD-WAN Branch CPE, cada link terminando em uma interface IP pública diferente do SD-WAN CPE. Os fornecedores de SD-WAN podem aproveitar esse recurso para selecionar o caminho ideal para o Azure, com base nas políticas de tráfego específicas para esses caminhos.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modelo de interconexão indireta

![Modelo de interconexão indireta](./media/sd-wan-connectivity-architecture/indirect.png)

Nesse modelo de arquitetura, SD-WAN Branch CPEs estão indiretamente conectados a hubs de WAN virtual. Como mostra a figura, um CPE virtual de WAN SD é implantado em uma VNet corporativa. Essa CPE virtual é, por sua vez, conectada aos hubs de WAN virtuais usando IPsec. A CPE virtual serve como um gateway SD-WAN no Azure. As ramificações que precisam acessar suas cargas de trabalho no Azure poderão acessá-las por meio do gateway v-CPE.

Como a conectividade com o Azure é por meio do gateway v-CPE (NVA), todo o tráfego de e para a carga de trabalho do Azure VNets para outras ramificações SD-WAN passam pelo NVA. Nesse modelo, o usuário é responsável por gerenciar e operar o SD-WAN NVA, incluindo alta disponibilidade, escalabilidade e roteamento.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modelo de WAN híbrido gerenciado

![Modelo de WAN híbrido gerenciado](./media/sd-wan-connectivity-architecture/hybrid.png)

Nesse modelo de arquitetura, as empresas podem aproveitar um serviço SD-WAN gerenciado oferecido por um parceiro MSP (provedor de serviços gerenciados). Esse modelo é semelhante aos modelos diretos ou indiretos descritos acima. No entanto, nesse modelo, o design, a orquestração e as operações do SD-WAN são entregues pelo provedor SD-WAN.

Os [parceiros msp de rede do Azure](../networking/networking-partners-msp.md) podem usar o [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) para implementar o SD-Wan e o serviço de WAN virtual na assinatura do Azure do cliente empresarial, bem como operar a WAN híbrida de ponta a ponta em nome do cliente. Esses MSPs podem também ser capazes de implementar o Azure ExpressRoute na WAN virtual e operá-lo como um serviço gerenciado de ponta a ponta.

## <a name="additional-information"></a>Informações adicionais

* [Incluir perguntas frequentes](virtual-wan-faq.md)
* [Solucionando a conectividade remota](work-remotely-support.md)