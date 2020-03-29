---
title: O que são parceiros de segurança confiáveis do Azure Firewall Manager (visualização)
description: Conheça os parceiros de segurança confiáveis do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436782"
---
# <a name="what-are-trusted-security-partners-preview"></a>O que são parceiros de segurança confiáveis (versão prévia)?

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

*Os parceiros de segurança confiáveis (preview)* no Azure Firewall Manager permitem que você use suas ofertas familiares, de segurança de terceiros como um serviço (SECaaS) para proteger o acesso à Internet para seus usuários.

Com uma configuração rápida, você pode proteger um hub com um parceiro de segurança suportado e rota ré e filtrar o tráfego da Internet a partir de suas Redes Virtuais (VNets) ou locais de filiais dentro de uma região. Isso é feito usando o gerenciamento automatizado de rotas, sem configurar e gerenciar UDRs (User Defined Routess, rotas definidas pelo usuário).

Você pode implantar hubs protegidos configurados com o parceiro de segurança de sua escolha em várias regiões do Azure para obter conectividade e segurança para seus usuários em qualquer lugar do mundo nessas regiões. Com a capacidade de usar a oferta do parceiro de segurança para tráfego de aplicativos Internet/SaaS e o Firewall Azure para tráfego privado nos hubs protegidos, agora você pode começar a construir sua vantagem de segurança no Azure que está perto de seus usuários e aplicativos distribuídos globalmente.

Para esta visualização, os parceiros de segurança suportados são **ZScaler** e **iboss**. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

![Parceiros de segurança confiáveis](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Cenários-chave

Você pode usar os parceiros de segurança para filtrar o tráfego da Internet nos seguintes cenários:

- Rede Virtual (VNet) para Internet

   Aproveite a proteção avançada da Internet com reconhecimento de usuário para suas cargas de trabalho na nuvem em execução no Azure.

- Ramificação para internet

   Aproveite sua conectividade do Azure e sua distribuição global para adicionar facilmente filtragem nsaas de terceiros para ramificação para cenários da Internet. Você pode construir sua rede de trânsito global e a borda de segurança usando o Azure Virtual WAN.

Os cenários a seguir têm suporte:
-   VNet para Internet através de uma oferta de parceiro de terceiros.
-   Ramifique-se à Internet através de uma oferta de parceiro de terceiros.
-   Ramifique-se à Internet através de uma oferta de parceiro de terceiros, o resto do tráfego privado (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) via Azure Firewall.

O seguinte cenário não é suportado:

- O VNet para internet através de uma oferta de parceiro não pode ser combinado com o Firewall Azure para tráfego privado. Veja as seguintes limitações.

## <a name="current-limitations"></a>Limitações atuais

- Para o VNet à Internet, você não pode misturar a adição do Firewall Azure para tráfego privado e uma oferta de parceiros para tráfego de Internet. Você pode enviar tráfego da Internet para o Azure Firewall ou um parceiro de segurança de terceiros oferecendo no hub virtual protegido, mas não ambos. 
- Você pode implantar no máximo um parceiro de segurança por hub virtual. Se você precisar alterar o provedor, você deve remover o parceiro existente e adicionar um novo.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Melhores práticas para filtragem de tráfego na Internet em hubs virtuais protegidos

O tráfego da Internet normalmente inclui tráfego da Web. Mas também inclui tráfego destinado a aplicativos SaaS como o Office 365 (O365) e serviços públicos paaS do Azure, como Azure Storage, Azure Sql, e assim por diante. A seguir, as recomendações de boas práticas para o manuseio do tráfego para esses serviços:

### <a name="handling-azure-paas-traffic"></a>Manipulação do tráfego do Azure PaaS
 
- Use o Firewall Do Azure para proteção se o seu tráfego for composto principalmente pelo Azure PaaS, e o acesso a recursos para seus aplicativos pode ser filtrado usando endereços IP, FQDNs, tags de serviço ou tags FQDN.

- Use uma solução de parceiro de terceiros em seus hubs se o tráfego for composto por acesso a aplicativos SaaS ou você precisar de filtragem com reconhecimento do usuário (por exemplo, para suas cargas de trabalho de infra-estrutura de desktop virtual (VDI) ou você precisar de recursos avançados de filtragem da Internet.

![Todos os cenários para O Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Tráfego do Handling Office 365 (O365)

Em cenários de localização de filiais distribuídos globalmente, você deve redirecionar o tráfego do Office 365 diretamente na agência antes de enviar o tráfego restante da Internet que seu hub protegido do Azure.

Para o Office 365, a latência e o desempenho da rede são fundamentais para uma experiência bem-sucedida do usuário. Para atingir esses objetivos em torno do desempenho ideal e da experiência do usuário, os clientes devem implementar a fuga direta e local do Office 365 antes de considerar o roteamento do resto do tráfego da Internet através do Azure.

[Os princípios de conectividade de rede do Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) exigem que as principais conexões de rede do Office 365 sejam roteadas localmente a partir do ramo de usuário ou dispositivo móvel e diretamente pela Internet para o ponto de presença da rede Microsoft mais próxima.

Além disso, as conexões do Office 365 são fortemente criptografadas para privacidade e usam protocolos eficientes e proprietários por razões de desempenho. Isso torna impraticável e impactante submeter essas conexões a soluções tradicionais de segurança em nível de rede. Por essas razões, recomendamos fortemente que os clientes enviem o tráfego do Office 365 diretamente das agências, antes de enviar o resto do tráfego através do Azure. A Microsoft fez parceria com vários provedores de soluções SD-WAN, que se integram ao Azure e ao Office 365 e facilitam que os clientes habilitem a saída direta e local da Internet do Office 365. Para obter [detalhes, veja como defina minhas políticas de O365 via WAN virtual?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Próximas etapas

[Implante uma oferta de segurança confiável em um hub seguro, usando o Azure Firewall Manager](deploy-trusted-security-partner.md).
