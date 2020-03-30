---
title: Arquitetura de referência corporativa para Azure DevTest Labs
description: Este artigo fornece orientação de arquitetura de referência para o Azure DevTest Labs em uma empresa.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132845"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Arquitetura de referência do Azure DevTest Labs para empresas
Este artigo fornece arquitetura de referência para ajudá-lo a implantar uma solução baseada no Azure DevTest Labs em uma empresa. Inclui o seguinte:
- Conectividade no local via Azure ExpressRoute
- Um gateway de desktop remoto para fazer login remotamente em máquinas virtuais
- Conectividade a um repositório de artefatos para artefatos privados
- Outros serviços paas que são usados em laboratórios

![Diagrama de arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os elementos-chave da arquitetura de referência:

- **Azure Active Directory (Azure AD)**: O DevTest Labs usa o [serviço Azure AD para gerenciamento de identidade](../active-directory/fundamentals/active-directory-whatis.md). Considere esses dois aspectos-chave quando você dá aos usuários acesso a um ambiente baseado no DevTest Labs:
    - **Gerenciamento de recursos**: Ele fornece acesso ao portal Azure para gerenciar recursos (criar máquinas virtuais; criar ambientes; iniciar, parar, reiniciar, excluir e aplicar artefatos; e assim por diante). O gerenciamento de recursos é feito no Azure usando o RBAC (Role-Based Access Control, controle de acesso baseado em função). Você atribui funções aos usuários e define permissões de recursos e nível de acesso.
    - **Máquinas virtuais (nível de rede)**: Na configuração padrão, as máquinas virtuais usam uma conta administrativa local. Se houver um domínio disponível[(Azure AD Domain Services,](../active-directory-domain-services/overview.md)um domínio on-premises ou um domínio baseado em nuvem), as máquinas podem ser unidas ao domínio. Os usuários podem então usar suas identidades baseadas em domínio para se conectar às VMs.
- **Conectividade no local**: Em nosso diagrama de arquitetura, [o ExpressRoute](../expressroute/expressroute-introduction.md) é usado. Mas você também pode usar uma [VPN site-to-site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora o ExpressRoute não seja necessário para o DevTest Labs, é comumente usado em empresas. O ExpressRoute só é necessário se você precisar de acesso aos recursos corporativos. Cenários comuns são:
    - Você tem dados no local que não podem ser movidos para a nuvem.
    - Você prefere juntar as máquinas virtuais do laboratório ao domínio local.
    - Você deseja forçar todo o tráfego de rede dentro e fora do ambiente da nuvem através de um firewall local para segurança/conformidade.
- **Grupos de segurança de rede**: Uma maneira comum de restringir o tráfego ao ambiente da nuvem (ou dentro do ambiente da nuvem) com base em endereços IP de origem e destino é usar um grupo de segurança de [rede](../virtual-network/security-overview.md). Por exemplo, você deseja permitir apenas o tráfego que se origina da rede corporativa para as redes do laboratório.
- **Gateway de desktop remoto**: As empresas normalmente bloqueiam conexões remotas de área de trabalho no firewall corporativo. Existem várias opções para habilitar a conectividade ao ambiente baseado em nuvem no DevTest Labs, incluindo:
  - Use um [gateway de desktop remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e permita o endereço IP estático do balanceador de carga do gateway.
  - [Direcione todo](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o tráfego RDP de entrada através da conexão VPN ExpressRoute/site-para-site. Essa funcionalidade é uma consideração comum quando as empresas planejam uma implantação do DevTest Labs.
- **Serviços de rede (redes virtuais, sub-redes)**: A topologia [de rede Do Azure](../networking/networking-overview.md) é outro elemento-chave na arquitetura DevTest Labs. Ele controla se os recursos do laboratório podem se comunicar e ter acesso ao local e à internet. Nosso diagrama de arquitetura inclui as maneiras mais comuns que os clientes usam o DevTest Labs: Todos os laboratórios se conectam via [peering](../virtual-network/virtual-network-peering-overview.md) de rede virtual usando um [modelo com fala de hub](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a conexão VPN ExpressRoute/site-to-site com as instalações locais. Mas o DevTest Labs usa diretamente o Azure Virtual Network, para que não haja restrições sobre como você configura a infra-estrutura de rede.
- **DevTest Labs**: DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte [Sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**: As máquinas virtuais são uma carga de trabalho fundamental que o DevTest Labs suporta junto com outros recursos do Azure. O DevTest Labs facilita e rápido para uma empresa fornecer acesso aos recursos do Azure (incluindo VMs e outros recursos do Azure). Saiba mais sobre o acesso ao Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores.](devtest-lab-test-env.md)

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade
Embora o DevTest Labs não tenha cotas ou limites incorporados, outros recursos do Azure que são [usados](../azure-resource-manager/management/azure-subscription-service-limits.md)na operação típica de um laboratório têm cotas de nível de assinatura . Assim, em uma implantação corporativa típica, você precisa de várias assinaturas do Azure para cobrir uma grande implantação do DevTest Labs. As cotas que as empresas mais freqatinantes alcançam são:

- **Grupos de recursos**: Na configuração padrão, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou o usuário cria um ambiente usando o serviço. As assinaturas podem conter [até 980 grupos de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Então, esse é o limite de máquinas e ambientes virtuais em uma assinatura. Existem duas outras configurações que você deve considerar:
    - **[Todas as máquinas virtuais vão para o mesmo grupo de recursos](resource-group-control.md)**: Embora essa configuração ajude você a atingir o limite do grupo de recursos, isso afeta o limite de tipo de recurso por grupo de recursos.
    - **Usando IPs Públicos Compartilhados**: Todas as VMs do mesmo tamanho e região vão para o mesmo grupo de recursos. Essa configuração é um "meio termo" entre cotas de grupos de recursos e cotas de grupo de recursos por grupo de recursos, se as máquinas virtuais tiverem endereços IP públicos.
- **Recursos por grupo de recursos por tipo de recurso**: O limite padrão para recursos por grupo de recursos por tipo de recurso é de [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando você usa *todas as VMs vão para a mesma* configuração de grupo de recursos, os usuários atingem esse limite de assinatura muito mais cedo, especialmente se as VMs tiverem muitos discos extras.
- **Contas de armazenamento**: Um laboratório no DevTest Labs vem com uma conta de armazenamento. A cota do Azure para [o número de contas de armazenamento por região por assinatura é de 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O número máximo de DevTest Labs na mesma região também é de 250.
- **Atribuições da função**: Uma atribuição de função é como você dá a um usuário ou principal acesso a um recurso (proprietário, recurso, nível de permissão). No Azure, há um [limite de 2.000 atribuições de função por assinatura.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Por padrão, o serviço DevTest Labs cria um grupo de recursos para cada VM. O proprietário recebe permissão *do proprietário* para a VM do DevTest Labs e a permissão *do leitor* para o grupo de recursos. Desta forma, cada nova VM que você cria usa duas atribuições de função, além das atribuições que são usadas quando você dá aos usuários permissão para o laboratório.
- **API lê/escreve**: Existem várias maneiras de automatizar o Azure e o DevTest Labs, incluindo APIs REST, PowerShell, Azure CLI e Azure SDK. Através da automação, você pode atingir outro limite nas solicitações de API: Cada assinatura permite até [12.000 solicitações de leitura e 1.200 solicitações de gravação por hora](../azure-resource-manager/management/request-limits-and-throttling.md). Esteja ciente desse limite quando automatizar o DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento
O DevTest Labs tem uma ótima interface de usuário administrativa para trabalhar com um único laboratório. Mas em uma empresa, você provavelmente tem várias assinaturas do Azure e muitos laboratórios. Fazer alterações consistentemente em todos os seus laboratórios requer scripting/automação. Aqui estão alguns exemplos e práticas de gerenciamento recomendadas para uma implantação do DevTest Labs:

- **Alterações nas configurações do laboratório**: Um cenário comum é atualizar uma configuração de laboratório específica em todos os laboratórios na implantação. Por exemplo, um novo tamanho de ocorrência de VM está disponível, e todos os laboratórios devem ser atualizados para permitir isso. É melhor automatizar essas alterações usando scripts PowerShell, CLI ou APIs REST.  
- **Token de acesso pessoal do repositório de artefatos**: Normalmente, os tokens de acesso pessoal para um repositório do Git expiram em 90 dias, um ano ou dois anos. Para garantir a continuidade, é importante estender o token de acesso pessoal ou criar um novo. Em seguida, use a automação para aplicar o novo token de acesso pessoal a todos os laboratórios.
- **Restringir alterações em uma configuração de laboratório**: Muitas vezes, uma configuração específica deve ser restrita (como permitir o uso de imagens de mercado). Você pode usar a Diretiva Azure para evitar alterações em um tipo de recurso. Ou você pode criar uma função personalizada e conceder aos usuários essa função em vez da função do *proprietário* para o laboratório. Você pode fazer isso para a maioria das configurações no laboratório (suporte interno, anúncio de laboratório, tamanhos de VM permitidos, e assim por diante).
- **Exija que as VMs sigam uma convenção de nomeação**: os gerentes geralmente querem identificar facilmente VMs que fazem parte de um ambiente de desenvolvimento e teste baseado em nuvem. Você pode fazer isso usando [a Política do Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante notar que o DevTest Labs usa recursos subjacentes do Azure que são gerenciados da mesma maneira: rede, discos, computação e assim por diante. Por exemplo, a Política Azure se aplica a máquinas virtuais que são criadas em laboratório. O Azure Security Center pode informar sobre a conformidade com a VM. E o serviço de backup do Azure pode fornecer backups regulares para as VMs no laboratório.

## <a name="security-considerations"></a>Considerações sobre segurança
O Azure DevTest Labs usa recursos existentes no Azure (computação, rede e assim por diante). Assim, ele se beneficia automaticamente dos recursos de segurança que são incorporados na plataforma. Por exemplo, para exigir que as conexões remotas de entrada de desktop se originem apenas da rede corporativa, basta adicionar um grupo de segurança de rede à rede virtual no gateway de desktop remoto. A única consideração adicional de segurança é o nível de permissões que você concede aos membros da equipe que usam os laboratórios no dia-a-dia. As permissões mais comuns são [ *proprietário* e *usuário.*](devtest-lab-add-devtest-user.md) Para obter mais informações sobre essas funções, consulte [Adicionar proprietários e usuários no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Próximas etapas
Veja o próximo artigo desta série: [Dimensione sua infra-estrutura do Azure DevTest Labs](devtest-lab-guidance-scale.md).
