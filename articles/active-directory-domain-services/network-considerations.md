---
title: Planejamento de rede e conexões para serviços de domínio Azure AD | Microsoft Docs
description: Conheça algumas das considerações e recursos de design de rede virtual usados para conectividade quando você executa os Serviços de Domínio do Diretório Ativo do Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264187"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Considerações de design de rede virtual e opções de configuração para serviços de domínio Azure AD

Como o Azure Active Directory Domain Services (AD DS) fornece serviços de autenticação e gerenciamento para outros aplicativos e cargas de trabalho, a conectividade de rede é um componente-chave. Sem recursos de rede virtuais configurados corretamente, aplicativos e cargas de trabalho não podem se comunicar e usar os recursos fornecidos pelo Azure AD DS. Planeje seus requisitos de rede virtual para garantir que o Azure AD DS possa atender seus aplicativos e cargas de trabalho conforme necessário.

Este artigo descreve considerações e requisitos de design para uma rede virtual Azure para suportar o Azure AD DS.

## <a name="azure-virtual-network-design"></a>Design de rede virtual do Azure

Para fornecer conectividade de rede e permitir que aplicativos e serviços se autentiquem contra o Azure AD DS, você usa uma rede e uma sub-rede virtuais do Azure. Idealmente, o Azure AD DS deve ser implantado em sua própria rede virtual. Você pode incluir uma sub-rede de aplicativos separada na mesma rede virtual para hospedar suas cargas de trabalho de vm ou aplicativos leves de gerenciamento. Uma rede virtual separada para cargas de trabalho de aplicativos maiores ou complexas, acompanhadas da rede virtual Azure AD DS, geralmente é o design mais apropriado. Outras opções de design são válidas, desde que você atenda aos requisitos descritos nas seguintes seções para a rede virtual e a sub-rede.

Ao projetar a rede virtual para o Azure AD DS, as seguintes considerações se aplicam:

* O Azure AD DS deve ser implantado na mesma região do Azure que sua rede virtual.
    * Neste momento, você só pode implantar um domínio gerenciado pelo Azure AD DS por inquilino Azure AD. O domínio gerenciado pelo Azure AD DS é implantado em uma única região. Certifique-se de criar ou selecionar uma rede virtual em uma [região que suporte o Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Considere a proximidade de outras regiões do Azure e as redes virtuais que hospedam suas cargas de trabalho de aplicativos.
    * Para minimizar a latência, mantenha seus aplicativos principais próximos ou na mesma região que a sub-rede virtual do seu domínio gerenciado pelo Azure AD DS. Você pode usar conexões de rede virtual ou vpn (virtual private network) entre redes virtuais do Azure. Essas opções de conexão são discutidas em uma seção a seguir.
* A rede virtual não pode contar com serviços de DNS que não os fornecidos pelo Azure AD DS.
    * O Azure AD DS fornece seu próprio serviço de DNS. A rede virtual deve ser configurada para usar esses endereços de serviço DNS. A resolução de nomes para espaços de nome adicionais pode ser realizada usando encaminhadores condicionais.
    * Você não pode usar configurações personalizadas do servidor DNS para direcionar consultas de outros servidores DNS, incluindo em VMs. Os recursos na rede virtual devem usar o serviço DNS fornecido pelo Azure AD DS.

> [!IMPORTANT]
> Você não pode mover o Azure AD DS para uma rede virtual diferente depois de habilitar o serviço.

Um domínio gerenciado pelo Azure AD DS se conecta a uma sub-rede em uma rede virtual Azure. Projete esta sub-rede para Azure AD DS com as seguintes considerações:

* O Azure AD DS deve ser implantado em sua própria sub-rede. Não use uma sub-rede existente ou uma sub-rede de gateway.
* Um grupo de segurança de rede é criado durante a implantação de um domínio gerenciado pelo Azure AD DS. Este grupo de segurança de rede contém as regras necessárias para a comunicação correta do serviço.
    * Não crie ou use um grupo de segurança de rede existente com suas próprias regras personalizadas.
* O Azure AD DS requer 3-5 endereços IP. Certifique-se de que o intervalo de endereços IP da sub-rede possa fornecer esse número de endereços.
    * Restringir os endereços IP disponíveis pode impedir que os Serviços de Domínio Ad do Azure mantenham dois controladores de domínio.

O diagrama de exemplo a seguir descreve um design válido onde o Azure AD DS tem sua própria sub-rede, há uma sub-rede de gateway para conectividade externa e as cargas de trabalho de aplicativos estão em uma sub-rede conectada dentro da rede virtual:

![Design de sub-rede recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Conexões à rede virtual Azure AD DS

Como observado na seção anterior, você só pode criar um domínio gerenciado do Azure AD Domain Services em uma única rede virtual no Azure, e apenas um domínio gerenciado pode ser criado por inquilino Azure AD. Com base nessa arquitetura, talvez seja necessário conectar uma ou mais redes virtuais que hospedam suas cargas de trabalho de aplicativos à sua rede virtual Azure AD DS.

Você pode conectar cargas de trabalho de aplicativos hospedadas em outras redes virtuais do Azure usando um dos seguintes métodos:

* Emparelhamento de rede virtual
* Rede virtual privada (VPN)

### <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

o emparelhamento de rede virtual é um mecanismo que conecta duas redes virtuais na mesma região por meio da rede principal do Azure. O peering de rede virtual global pode conectar rede virtual em regiões do Azure. Uma vez peered, as duas redes virtuais permitem que recursos, como VMs, se comuniquem entre si usando endereços IP privados. O uso de peering de rede virtual permite que você implante um domínio gerenciado pelo Azure AD DS com as cargas de trabalho de aplicativos implantadas em outras redes virtuais.

![Conectividade de rede virtual usando emparelhamento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Para obter mais informações, consulte [a visão geral da rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Rede Virtual Privada (VPN)

Você pode conectar uma rede virtual a outra rede virtual (VNet-to-VNet) da mesma forma que você pode configurar uma rede virtual em um local local local. Ambas as conexões usam um gateway VPN para criar um túnel seguro usando IPsec/IKE. Esse modelo de conexão permite que você implante o Azure AD DS em uma rede virtual do Azure e, em seguida, conecte locais no local ou outras nuvens.

![Conectividade de rede virtual usando um Gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Para obter mais informações sobre o uso de rede privada virtual, leia [Configure uma conexão de gateway VNet-to-VNet VPN usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Resolução de nomes ao conectar redes virtuais

As redes virtuais conectadas à rede virtual Azure AD Domain Services normalmente têm suas próprias configurações de DNS. Quando você conecta redes virtuais, ela não configura automaticamente a resolução de nomes para a rede virtual de conexão para resolver serviços fornecidos pelo domínio gerenciado pelo Azure AD DS. A resolução de nomes nas redes virtuais de conexão deve ser configurada para permitir que as cargas de trabalho do aplicativo localizem os Serviços de Domínio AD do Azure.

Você pode habilitar a resolução de nomes usando reencaminhadores DNS condicionais no servidor DNS que suportam as redes virtuais de conexão ou usando os mesmos endereços IP DNS da rede virtual Azure AD Domain Service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Recursos de rede usados pelo Azure AD DS

Um domínio gerenciado pelo Azure AD DS cria alguns recursos de rede durante a implantação. Esses recursos são necessários para uma operação e gerenciamento bem-sucedidos do domínio gerenciado pelo Azure AD DS e não devem ser configurados manualmente.

| Recursos do Azure                          | Descrição |
|:----------------------------------------|:---|
| Cartão de interface de rede                  | O Azure AD DS hospeda o domínio gerenciado em dois controladores de domínio (DCs) que são executados no Windows Server como VMs do Azure. Cada VM tem uma interface de rede virtual que se conecta à sua sub-rede virtual. |
| Endereço IP público padrão dinâmico      | O Azure AD DS se comunica com o serviço de sincronização e gerenciamento usando um endereço IP público Padrão SKU. Para obter mais informações sobre endereços IP públicos, consulte [os tipos de endereçoIP e os métodos de alocação no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Balanceador de carga padrão Azure            | O Azure AD DS usa um balanceador de carga SKU padrão para tradução de endereço de rede (NAT) e balanceamento de carga (quando usado com LDAP seguro). Para obter mais informações sobre os balanceadores de carga do Azure, consulte [O que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Regras de tradução de endereços de rede (NAT) | O Azure AD DS cria e usa três regras NAT no balanceador de carga - uma regra para tráfego HTTP seguro e duas regras para o powershell seguro. |
| Regras do balanceador de carga                     | Quando um domínio gerenciado pelo Azure AD DS é configurado para LDAP seguro na porta TCP 636, três regras são criadas e usadas em um balanceador de carga para distribuir o tráfego. |

> [!WARNING]
> Não exclua nenhum dos recursos de rede criados pelo Azure AD DS. Se você excluir qualquer um dos recursos da rede, ocorrerá uma paralisação do serviço Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Grupos de segurança de rede e portas necessárias

Um grupo de segurança de [rede (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contém uma lista de regras que permitem ou negam o tráfego de rede para o tráfego em uma rede virtual Azure. Um grupo de segurança de rede é criado quando você implanta o Azure AD DS que contém um conjunto de regras que permitem que o serviço forneça funções de autenticação e gerenciamento. Esse grupo de segurança de rede padrão está associado à sub-rede virtual em que o domínio Gerenciado pelo Azure AD DS é implantado.

As seguintes regras do grupo de segurança de rede são necessárias para que o Azure AD DS forneça serviços de autenticação e gerenciamento. Não edite ou exclua essas regras do grupo de segurança de rede para a sub-rede virtual em que o domínio Gerenciado pelo Azure AD DS é implantado.

| Número da porta | Protocolo | Fonte                             | Destino | Ação | Obrigatório | Finalidade |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Qualquer         | Allow  | Sim      | Sincronização com seu inquilino Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Qualquer         | Allow  | Sim      | Gestão do seu domínio. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Qualquer         | Allow  | Sim      | Gestão do seu domínio. |
| 636         | TCP      | Qualquer                                | Qualquer         | Allow  | Não       | Somente ativado quando você configura LDAP (LDAPs) seguro. |

> [!WARNING]
> Não edite manualmente esses recursos e configurações de rede. Quando você associa um grupo de segurança de rede mal configurado ou uma tabela de rota definida pelo usuário com a sub-rede na qual o Azure AD DS é implantado, você pode interromper a capacidade da Microsoft de fazer o serviço e gerenciar o domínio. A sincronização entre o seu inquilino Azure AD e o domínio gerenciado do Azure AD DS também é interrompida.
>
> As regras padrão para *AllowVnetInBound,* *AllowAzureLoadBalancerInBound,* *DenyAllInBound,* *AllowVnetOutBound,* *AllowInternetOutBound*e *DenyAllOutBound* também existem para o grupo de segurança da rede. Não edite ou exclua essas regras padrão.
>
> O SLA do Azure não se aplica a implantações onde um grupo de segurança de rede configurado incorretamente e/ou tabelas de rota definidas pelo usuário foram aplicadas que bloqueiam o Azure AD DS de atualizar e gerenciar seu domínio.

### <a name="port-443---synchronization-with-azure-ad"></a>Porta 443 - sincronização com Azure AD

* Usado para sincronizar seu inquilino Azure AD com seu domínio gerenciado pelo Azure AD DS.
* Sem acesso a esta porta, seu domínio Gerenciado pelo Azure AD DS não pode sincronizar com o seu inquilino Azure AD. Os usuários podem não ser capazes de fazer login, pois as alterações em suas senhas não seriam sincronizadas com o domínio gerenciado pelo Azure AD DS.
* O acesso de entrada a essa porta a endereços IP é restrito por padrão usando a tag de serviço **AzureActiveDirectoryDomainServices.**
* Não restringir o acesso de saída desta porta.

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389 - gerenciamento usando desktop remoto

* Usado para conexões remotas de desktop para controladores de domínio no domínio gerenciado pelo Azure AD DS.
* A regra padrão do grupo de segurança de rede usa a tag de serviço *CorpNetSaw* para restringir ainda mais o tráfego.
    * Essa tag de serviço permite apenas que estações de trabalho de acesso seguro na rede corporativa da Microsoft usem desktop remoto para o domínio gerenciado pelo Azure AD DS.
    * O acesso só é permitido com justificativa de negócios, como para gerenciamento ou cenários de solução de problemas.
* Esta regra pode ser definida como *Negar*, e somente definida como *Permitir* quando necessário. A maioria das tarefas de gerenciamento e monitoramento são executadas usando o PowerShell remoting. O RDP só é usado no raro caso em que a Microsoft precisa se conectar remotamente ao seu domínio gerenciado para solução avançada de problemas.

> [!NOTE]
> Você não pode selecionar manualmente a tag de serviço *CorpNetSaw* no portal se você tentar editar essa regra do grupo de segurança da rede. Você deve usar o Azure PowerShell ou o Azure CLI para configurar manualmente uma regra que usa a tag de serviço *CorpNetSaw.*

### <a name="port-5986---management-using-powershell-remoting"></a>Porta 5986 - gerenciamento usando powershell remoting

* Usado para executar tarefas de gerenciamento usando o PowerShell remoting em seu domínio gerenciado pelo Azure AD DS.
* Sem acesso a esta porta, o domínio gerenciado pelo Azure AD DS não pode ser atualizado, configurado, feito backup ou monitorado.
* Para domínios gerenciados pelo Azure AD DS que usam uma rede virtual baseada no Gerenciador de recursos, você pode restringir o acesso de entrada a essa porta à tag de serviço *AzureActiveDirectoryDomainServices.*
    * Para domínios gerenciados do Azure AD DS legados usando uma rede virtual baseada em clássico, você pode restringir o acesso de entrada a esta porta aos seguintes endereços IP de origem: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, e *104.40.87.209*.

    > [!NOTE]
    > Em 2017, o Azure AD Domain Services tornou-se disponível para hospedagem em uma rede Azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro usando os recursos modernos do Azure Resource Manager. Como as implantações do Azure Resource Manager substituem totalmente as implantações clássicas, as implantações clássicas de rede virtual do Azure AD DS serão retiradas em 1º de março de 2023.
    >
    > Para obter mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Rotas definidas pelo usuário

As rotas definidas pelo usuário não são criadas por padrão e não são necessárias para que o Azure AD DS funcione corretamente. Se você for obrigado a usar tabelas de rota, evite fazer alterações na rota *0.0.0.0.* Alterações nessa rota interrompem os Serviços de Domínio Azure AD e colocam o domínio gerenciado em um estado sem suporte.

Você também deve direcionar o tráfego de entrada dos endereços IP incluídos nas respectivas tags de serviço do Azure para a sub-rede Azure AD Domain Services. Para obter mais informações sobre as tags de serviço e seu endereço IP associado, consulte [Azure IP Ranges and Service Tags - Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Esses intervalos de IP do datacenter do Azure podem ser alterar sem aviso prévio. Certifique-se de ter processos para validar, você tem os endereços IP mais recentes.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre alguns dos recursos de rede e opções de conexão usados pelo Azure AD DS, consulte os seguintes artigos:

* [Peering de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
* [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Grupos de segurança da rede Azure](../virtual-network/security-overview.md)
