---
title: 'Arquitetura: Migrar para o Azure Virtual WAN'
description: Saiba como migrar para o Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062894"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrar para a WAN Virtual do Azure

O Azure Virtual WAN permite que as empresas simplifiquem sua conectividade global para se beneficiar da escala da rede global da Microsoft. Este artigo fornece detalhes técnicos para empresas que desejam migrar de uma topologia de hub e spoke gerenciada pelo cliente existente para um design que aproveita os hubs de WAN virtuais gerenciados pela Microsoft.

Para obter informações sobre os benefícios que o Azure Virtual WAN permite para empresas que adotam uma rede global corporativa moderna centrada na nuvem, consulte [a arquitetura da rede de trânsito global e a WAN virtual](virtual-wan-global-transit-network-architecture.md).

![hub e](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
falou**Figura: Azure Virtual WAN**

O modelo de conectividade de hub and spoke do Azure Virtual Datacenter (VDC) foi adotado por milhares de nossos clientes para aproveitar o comportamento de roteamento transitivo padrão do Azure Networking, a fim de construir redes de nuvem simples e escaláveis. O Azure Virtual WAN baseia-se nesses conceitos e introduz novos recursos que permitem topologias globais de conectividade, não apenas entre locais locais e Azure, mas também permitindo que os clientes aproveitem a escala da rede Microsoft para aumentar sua redes globais existentes.

Este artigo mostra como migrar um ambiente híbrido existente para a WAN Virtual.

## <a name="scenario"></a>Cenário

Contoso é uma organização financeira global com escritórios na Europa e ásia. Eles estão planejando migrar seus aplicativos existentes de um data center local para o Azure e criaram um design básico baseado na arquitetura do VDC, incluindo redes virtuais regionais de hub gerenciadas pelo cliente para conectividade híbrida. Como parte da mudança para tecnologias baseadas em nuvem, a equipe de rede tem sido encarregada de garantir que sua conectividade seja otimizada para o negócio avançar.

A figura a seguir mostra uma visão de alto nível da rede global existente, incluindo conectividade a várias regiões do Azure.

![Contoso topologia](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
de rede existente**Figura: Contoso topologia de rede existente**

Os pontos seguintes podem ser compreendidos da topologia de rede existente:

- Uma topologia hub e spoke é usada em várias regiões, incluindo circuitos Premium do ExpressRoute para conectividade de volta para uma WAN privada comum.

- Alguns desses sites também têm túneis VPN ligados diretamente ao Azure para alcançar aplicativos hospedados dentro da nuvem Microsoft.

## <a name="requirements"></a>Requisitos

A equipe de rede ficou encarregada de fornecer um modelo de rede global capaz de dar suporte à migração da Contoso para a nuvem e otimizar as áreas de custo, escala e desempenho. Em resumo, os seguintes requisitos devem ser atendidos:

- Fornecer à sede social (HQ) e às branches um caminho otimizado para aplicativos hospedados na nuvem.
- Remova a dependência dos data centers locais (DC) existentes para o término do VPN, mantendo os seguintes caminhos de conectividade:
  - **Branch -to- VNet**: Escritórios conectados vpn devem ser capazes de acessar aplicativos migrados para a nuvem na região local do Azure.
  - **Branch -to-Hub -to-Hub -to-VNet**: Escritórios conectados com VPN devem ser capazes de acessar aplicativos migrados para a nuvem na região remota do Azure.
  - **Branch -to-branch**: Escritórios conectados com VPN regionais devem ser capazes de se comunicar entre si e sites de HQ/DC conectados da ExpressRoute.
  - **Ramo -to-Hub -to-Hub -to-branch**: Escritórios conectados com VPN separados globalmente devem ser capazes de se comunicar entre si e com quaisquer sites de HQ/DC conectados da ExpressRoute.
  - **Ramificação -to- Internet**: Sites conectados devem ser capazes de se comunicar com a Internet. Este tráfego deve ser filtrado e registrado.
  - **VNet -to-VNet**: As redes virtuais faladas na mesma região devem ser capazes de se comunicar entre si.
  - **VNet -to-Hub -to-Hub -to-VNet**: As redes virtuais faladas nas diferentes regiões devem ser capazes de se comunicar entre si.
- Fornecer a capacidade para os usuários roaming contoso (laptop e telefone) para acessar os recursos da empresa enquanto não está na rede corporativa.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Arquitetura WAN Virtual Do Azure

A figura a seguir mostra uma visão de alto nível da topologia de destino atualizada usando o Azure Virtual WAN para atender aos requisitos detalhados na seção anterior.

![Arquitetura WAN](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
virtual contoso**Figura: arquitetura WAN virtual do Azure**

Resumo:

- a HQ na Europa permanece conectada ao ExpressRoute, os DCs locais da Europa são totalmente migrados ao Azure e agora desativados.
- Os DCs e a HQ da Ásia permanecem conectados à WAN Particular. O Azure Virtual WAN agora é usado para aumentar a rede de operadoras locais e fornecer conectividade global.
- Hubs WAN virtuais do Azure implantados nas regiões do Azure e do Sudeste Asiático para fornecer hub de conectividade para dispositivos conectados expressRoute e VPN.
- Os hubs também fornecem a terminação de VPN para usuários móveis em vários tipos de clientes, usando a conectividade OpenVPN com a rede da malha global, permitindo acesso não apenas aos aplicativos migrados para o Azure, mas também a todos os recursos restantes no local.
- Conectividade com a Internet para recursos dentro de uma rede virtual fornecida pela WAN Virtual do Azure.

Conectividade com a Internet para sites remoto também fornecida pela WAN Virtual do Azure. Análise de Internet local com suporte por meio da integração de parceiro para acesso otimizado a serviços SaaS, como o Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrar para a WAN Virtual

Esta seção mostra as várias etapas para migrar para o Azure Virtual WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Passo 1: Região única do hub e do vdc

Reveja a arquitetura. A figura a seguir mostra uma topologia de região única para Contoso antes do lançamento do Azure Virtual WAN:

![Topologia de](./media/migrate-from-hub-spoke-topology/figure1.png)
região única**Figura 1: Região única de hub-and-spoke vDC**

De acordo com a abordagem do Virtual Data Center (VDC), a rede virtual do hub gerenciada pelo cliente contém vários blocos de funções:

- Serviços compartilhados (qualquer função comum exigida por vários raios). Exemplo: Contoso usa controladores de domínio do Windows Server em máquinas virtuais de Infra-estrutura como serviço (IaaS).
- Os serviços de firewall de IP/roteamento são fornecidos por uma solução de virtualização de rede de terceiros que permite o roteamento de IP de camada 3 spoke para spoke.
- Serviços de entrada/saída da Internet, incluindo o Gateway de Aplicativo do Azure para solicitações HTTPS de entrada e serviços de proxy de terceiros em execução em máquinas virtuais para acesso de saída filtrado aos recursos da Internet.
- Gateway de rede virtual ExpressRoute e VPN para conectividade com redes locais.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Passo 2: Implantar hubs WAN virtuais

Implante um hub WAN virtual em cada região. Configure o hub WAN virtual com VPN Gateway e ExpressRoute Gateway conforme descrito nos seguintes artigos:

- [Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure](virtual-wan-site-to-site-portal.md)
- [Tutorial: Crie uma associação ExpressRoute usando o Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> O Azure Virtual WAN deve estar usando o SKU padrão para habilitar algumas das rotas de tráfego mostradas neste artigo.

![Implantar hubs](./media/migrate-from-hub-spoke-topology/figure2.png)
WAN virtuais**Figura 2: Hub-and-spoke do VDC para migração de WAN virtual**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Passo 3: Conecte sites remotos (ExpressRoute e VPN) à WAN virtual

Conecte o hub WAN virtual aos circuitos ExpressRoute existentes e configure VPNs site-to-site pela Internet a quaisquer filiais remotas.

> [!NOTE]
> Os circuitos do ExpressRoute devem ser atualizados para o tipo de SKU Premium para se conectarem ao hub da WAN Virtual.

![Conecte sites remotos à WAN](./media/migrate-from-hub-spoke-topology/figure3.png)
Virtual Figura**3: hub-and-spoke do VDC à migração de WAN virtual**

Neste ponto, os equipamentos de rede no local começarão a receber rotas que refletem o espaço de endereço IP atribuído ao VNet do hub virtual gerenciado pela WAN. As filiais conectadas à VPN remota nesta fase verão dois caminhos para todos os aplicativos existentes nas redes virtuais spoke. Esses dispositivos devem ser configurados para continuar a usar o túnel para o Hub VDC para garantir o roteamento simétrico durante a fase de transição.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Passo 4: Teste a conectividade híbrida via WAN Virtual

Antes de usar o hub WAN virtual gerenciado para conectividade de produção, recomendamos que você configure uma rede virtual de teste e conexão Virtual WAN VNet. Valide se as conexões com esse ambiente de teste funcionam por meio do ExpressRoute e da VPN site a site antes de continuar com as próximas etapas.

![Teste a conectividade híbrida](./media/migrate-from-hub-spoke-topology/figure4.png)
via WAN Virtual**Figura 4: Hub-and-spoke do VDC para migração WAN virtual**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Passo 5: Conectividade de transição para hub WAN virtual

![Conectividade de transição](./media/migrate-from-hub-spoke-topology/figure5.png)
para hub WAN virtual**Figura 5: VDC hub-and-spoke para migração WAN virtual**

**a**. Exclua as conexões de peering existentes das redes virtuais Spoke para o antigo hub VDC. O acesso aos aplicativos nas redes virtuais spoke fica indisponível até que as etapas de a-c sejam concluídas.

**b**. Conecte as redes virtuais faladas ao hub WAN virtual através de conexões VNet.

**c**. Remova todas as UDR (rotas definidas pelo usuário) usadas anteriormente em redes virtuais spoke para comunicações spoke a spoke. Agora esse caminho está habilitado pelo roteamento dinâmico disponível no hub da WAN Virtual.

**d**. Os Gateways de ExpressRoute e de VPN existentes no hub do VDC agora estão desativados para permitir a próxima etapa (e).

**e**. Conecte o antigo Hub do VDC (hub de rede virtual) ao hub da WAN Virtual por meio de uma nova conexão VNet.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Passo 6: Antigo hub se torna serviços compartilhados falados

Agora, reprojetamos nossa rede do Azure para tornar o Hub da WAN Virtual o ponto central em nossa nova topologia.

![Antigo hub se torna](./media/migrate-from-hub-spoke-topology/figure6.png)
Serviços Compartilhados falou**Figura 6: VDC hub-and-spoke para migração WAN virtual**

Como o hub WAN virtual é uma entidade gerenciada e não permite a implantação de recursos personalizados, como máquinas virtuais, o bloco de serviços compartilhados agora existe como uma rede virtual falada e hospeda funções como entrada na Internet via Azure Application Gateway ou aparelho virtualizado de rede. O tráfego entre o ambiente de serviços compartilhados e as máquinas virtuais de back-end agora transita o hub gerenciado da WAN Virtual.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Passo 7: Otimize a conectividade no local para utilizar totalmente a WAN Virtual

Nesta fase, a Contoso já concluiu a maior parte de suas migrações de aplicativos de negócios para dentro do Microsoft Cloud, restando apenas alguns aplicativos herdados dentro do DC local.

![Otimize a conectividade no local](./media/migrate-from-hub-spoke-topology/figure7.png)
para utilizar totalmente a WAN Virtual**Figura 7: hub-and-spoke do VDC para migração de WAN virtual**

Para aproveitar a funcionalidade completa da WAN Virtual do Azure, a Contoso decide desativar suas conexões VPN local herdadas. Qualquer branch que continuar a acessar as redes da HQ ou do DC será capaz de transitar a rede global da Microsoft usando o roteamento de trânsito interno da WAN Virtual do Azure.

> [!NOTE]
> O Alcance Global do ExpressRoute é uma opção alternativa para os clientes que desejam aproveitar o backbone Microsoft para complementar suas WANs particulares existentes.

## <a name="end-state-architecture-and-traffic-paths"></a>Arquitetura de estado final e caminhos de tráfego

![Arquitetura de estado final](./media/migrate-from-hub-spoke-topology/figure8.png)
e caminhos de tráfego**Figura: WAN virtual da região dupla**

Esta seção fornece um resumo de como essa topologia atende aos requisitos originais examinando alguns fluxos de tráfego de exemplo.

### <a name="path-1"></a>Caminho 1

O Path 1 mostra o fluxo de tráfego de uma filial conectada ao S2S VPN na Ásia para um Azure VNet na região do Sudeste Asiático.

O tráfego é roteado da seguinte maneira:

- A filial da Ásia está conectada através de túneis resistentes habilitados para S2S BGP no centro WAN virtual do Sudeste Asiático.

- O hub da WAN Virtual da Ásia roteia o tráfego localmente para a VNet conectada.

![Fluxo 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Caminho 2

O path 2 mostra o fluxo de tráfego do ExpressRoute conectado ao QG europeu para um Azure VNet na região do Sudeste Asiático.

O tráfego é roteado da seguinte maneira:

- O QG europeu é conectado via circuito ExpressRoute premium no hub WAN Virtual da Europa Ocidental.

- A conectividade global de Hub para Hub da WAN Virtual permite o trânsito de tráfego para a VNet conectada na região remota.

![Fluxo 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Caminho 3

O path 3 mostra o fluxo de tráfego da Ásia no local DC conectado ao Private WAN a um Ramo Europeu S2S conectado.

O tráfego é roteado da seguinte maneira:

- o DC da Ásia está conectado à operadora de WAN Particular local.

- O circuito ExpressRoute termina localmente em WAN privado conecta-se ao hub WAN Virtual do Sudeste Asiático.

- A conectividade global de hub-to-hub wan virtual permite o trânsito.

![Fluxo 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Caminho 4

O Path 4 mostra o fluxo de tráfego de um Azure VNet na região do Sudeste Asiático para um Azure VNet na região da Europa Ocidental.

O tráfego é roteado da seguinte maneira:

- A conectividade global hub a Hub da WAN Virtual permite o trânsito nativo de todas as VNets do Azure conectadas sem configurações adicionais do usuário.

![Fluxo 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Caminho 5

O Path 5 mostra o fluxo de tráfego de usuários de VPN (P2S) roaming para um Azure VNet na região da Europa Ocidental.

O tráfego é roteado da seguinte maneira:

- Os usuários de laptops e dispositivos móveis usam o cliente OpenVPN para conectividade transparente no gateway P2S VPN na Europa Ocidental.

- O hub da WAN Virtual do Oeste da Europa roteia o tráfego localmente para a VNet conectada.

![Fluxo 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Controle de segurança e políticas por meio do Firewall do Azure

Contoso validou agora a conectividade entre todos os ramos e vNets de acordo com os requisitos discutidos anteriormente neste artigo. Para atender aos seus requisitos de controle de segurança e isolamento da rede, eles precisam continuar a separar e registrar o tráfego através da rede do hub. Anteriormente, essa função era executada por um aparelho virtual de rede (NVA). A Contoso também quer desativar seus serviços de proxy existentes e utilizar serviços nativos do Azure para filtragem de Internet de saída.

![Controle de segurança e políticas](./media/migrate-from-hub-spoke-topology/security-policy.png)
via Azure Firewall**Figura: Firewall Azure em WAN Virtual (hub virtual protegido)**

As etapas de alto nível a seguir são necessárias para introduzir o Firewall Azure nos hubs WAN virtuais para permitir um ponto unificado de controle de políticas. Para obter mais informações sobre esse processo e o conceito de Secure Virtual Hubs, consulte [O Azure Firewall Manager](../firewall-manager/index.yml).

1. Crie uma Política de Firewall do Azure.
2. Vincule uma política de firewall ao hub da WAN Virtual do Azure. Essa etapa permite que o hub WAN Virtual existente funcione como um hub virtual protegido e implante os recursos necessários do Firewall Azure.

> [!NOTE]
> Se o Firewall Azure for implantado em um hub WAN virtual padrão (SKU : Padrão): as políticas V2V, B2V, V2I e B2I FW só serão aplicadas no tráfego originário dos VNets e Filiais conectados ao hub específico onde o Azure FW é implantado (Secured Hub). O tráfego originário de VNets e Agências remotas que estão conectados a outros hubs WAN virtuais na mesma WAN virtual não será "protegido por firewall", mesmo que as agências remotas e o VNet estejam interconectados via hub WAN virtual para links de hub. O suporte de firewall cross-hub está no roteiro do Azure Virtual WAN e do Firewall Manager.

Os seguintes caminhos mostram os caminhos de conectividade habilitados usando hubs virtuais protegidos pelo Azure:

### <a name="path-6"></a>Caminho 6

O caminho 6 mostra fluxo de tráfego seguro entre VNets dentro da mesma região.

O tráfego é roteado da seguinte maneira:

- as Redes Virtuais conectadas ao mesmo Hub Virtual Seguro agora roteiam o tráfego por meio do Firewall do Azure.

- O Firewall do Azure pode aplicar políticas a esses fluxos.

![Fluxo 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Caminho 7

O Path 7 mostra o fluxo de tráfego de um VNet do Azure para a Internet ou serviço de segurança de terceiros.

O tráfego é roteado da seguinte maneira:

- as Redes Virtuais conectadas ao Hub Virtual Seguro podem enviar tráfego para destinos públicos na Internet, usando o Hub Seguro como um ponto central de acesso à Internet.

- Esse tráfego pode ser filtrado localmente usando as regras do Azure Firewall FQDN ou enviado para um serviço de segurança de terceiros para inspeção.

![Fluxo 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Caminho 8

O path 8 mostra o fluxo de tráfego de branch-to-Internet ou serviço de segurança de terceiros.

O tráfego é roteado da seguinte maneira:

- As agências conectadas ao Secure Virtual Hub podem enviar tráfego para destinos públicos na Internet usando o Secure Hub como ponto central de acesso à Internet.

- Esse tráfego pode ser filtrado localmente usando as regras do Azure Firewall FQDN ou enviado para um serviço de segurança de terceiros para inspeção.

![Fluxo 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [WAN Virtual do Azure](virtual-wan-about.md)
