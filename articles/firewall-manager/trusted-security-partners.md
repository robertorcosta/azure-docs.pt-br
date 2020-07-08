---
title: O que são provedores de parceiros de segurança do Azure firewall Manager?
description: Saiba mais sobre os provedores de parceiros de segurança do Gerenciador de firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563613"
---
# <a name="what-are-security-partner-providers"></a>O que são provedores de parceiro de segurança?

Os *provedores de parceiros de segurança* no Gerenciador de firewall do Azure permitem que você use suas ofertas de SECaaS (segurança como serviço) mais conhecidas e de terceiros para proteger o acesso à Internet para seus usuários.

Com uma configuração rápida, você pode proteger um hub com um parceiro de segurança com suporte e rotear e filtrar o tráfego de Internet de suas redes virtuais (VNets) ou locais de ramificação em uma região. Você pode fazer isso com o gerenciamento automatizado de rotas, sem configurar e gerenciar UDRs (rotas definidas pelo usuário).

Você pode implantar hubs protegidos configurados com o parceiro de segurança de sua escolha em várias regiões do Azure para obter conectividade e segurança para seus usuários em qualquer lugar do mundo nessas regiões. Com a capacidade de usar a oferta do parceiro de segurança para o tráfego de aplicativos de Internet/SaaS e o Firewall do Azure para tráfego privado nos hubs protegidos, agora você pode começar a criar sua borda de segurança no Azure que está perto de seus usuários e aplicativos distribuídos globalmente.

Os parceiros de segurança com suporte são **ZScaler**, **Check Point** (visualização) e **iboss** (Preview).

![Provedores de parceiro de segurança](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Principais cenários

Você pode usar os parceiros de segurança para filtrar o tráfego de Internet nos seguintes cenários:

- Rede virtual (VNet) para a Internet

   Aproveite a proteção avançada da Internet com reconhecimento de usuário para suas cargas de trabalho na nuvem em execução no Azure.

- Branch para a Internet

   Aproveite sua conectividade do Azure e a distribuição global para adicionar facilmente filtragem de NSaaS de terceiros para cenários de Branch para Internet. Você pode criar sua rede de trânsito global e borda de segurança usando a WAN virtual do Azure.

Os cenários a seguir têm suporte:
- VNet/Branch para a Internet por meio de um provedor de parceiros de segurança e o outro tráfego (spoke para spoke, spoke para Branch, ramificação para spoke) por meio do firewall do Azure.
- VNet/Branch para Internet por meio do provedor de parceiros de segurança

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Práticas recomendadas para filtragem de tráfego de Internet em hubs virtuais protegidos

O tráfego da Internet normalmente inclui o tráfego da Web. Mas também inclui o tráfego destinado a aplicativos SaaS como o Office 365 (O365) e os serviços de PaaS públicos do Azure, como o armazenamento do Azure, o SQL do Azure e assim por diante. Veja a seguir as recomendações de práticas recomendadas para lidar com o tráfego para esses serviços:

### <a name="handling-azure-paas-traffic"></a>Lidando com o tráfego de PaaS do Azure
 
- Use o Firewall do Azure para proteção se seu tráfego consistir principalmente da PaaS do Azure e o acesso aos recursos para seus aplicativos puder ser filtrado usando endereços IP, FQDNs, marcas de serviço ou marcas de FQDN.

- Use uma solução de parceiro de terceiros nos hubs se seu tráfego consistir em acesso ao aplicativo SaaS ou se você precisar de filtragem de reconhecimento de usuário (por exemplo, para suas cargas de trabalho de VDI (Virtual Desktop Infrastructure)) ou precisar de recursos avançados de filtragem da Internet.

![Todos os cenários do Gerenciador de firewall do Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Lidando com o tráfego do Office 365 (O365)

Em cenários de localização de Branch distribuídos globalmente, você deve redirecionar o tráfego do Office 365 diretamente no Branch antes de enviar o tráfego de Internet restante para o Hub protegido do Azure.

Para o Office 365, a latência de rede e o desempenho são essenciais para uma experiência de usuário bem-sucedida. Para atingir essas metas em relação ao desempenho e à experiência do usuário ideais, os clientes devem implementar o Office 365 Direct e o escape local antes de considerar o roteamento do restante do tráfego da Internet por meio do Azure.

Os [princípios de conectividade de rede do office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) chamam as principais conexões de rede do Office 365 para serem roteadas localmente da ramificação do usuário ou do dispositivo móvel e diretamente pela Internet para o ponto de presença mais próximo da rede da Microsoft.

Além disso, as conexões do Office 365 são criptografadas para privacidade e uso de protocolos proprietários e eficientes por motivos de desempenho. Isso torna impraticável e impactado a entidade dessas conexões com soluções de segurança de nível de rede tradicionais. Por esses motivos, é altamente recomendável que os clientes enviem o tráfego do Office 365 diretamente de ramificações, antes de enviar o restante do tráfego por meio do Azure. A Microsoft fez um parceria com vários provedores de soluções SD-WAN, que se integram ao Azure e ao Office 365 e facilitam para os clientes a análise do Office 365 Direct e da Internet local. Para obter detalhes, consulte [como fazer definir minhas políticas de O365 por meio da WAN virtual?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>Próximas etapas

[Implante uma oferta de parceiro de segurança em um hub protegido usando o Gerenciador de firewall do Azure](deploy-trusted-security-partner.md).
