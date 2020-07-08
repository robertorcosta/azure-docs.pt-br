---
title: 'Arquitetura: migrar para a WAN virtual do Azure'
description: Saiba como migrar para a WAN virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8dfcdd8195824cb732df2c0c70c338e69630c5cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753132"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrar para a WAN Virtual do Azure

A WAN virtual do Azure permite que as empresas simplifiquem sua conectividade global para se beneficiarem da escala da rede global da Microsoft. Este artigo fornece detalhes técnicos para empresas que desejam migrar de uma topologia hub e spoke gerenciada pelo cliente existente, para um design que aproveita os hubs de WAN virtuais gerenciados pela Microsoft.

Para obter informações sobre os benefícios que a WAN virtual do Azure permite que as empresas adotem uma rede global corporativa moderna centrada em nuvem, consulte [arquitetura de rede de trânsito global e WAN virtual](virtual-wan-global-transit-network-architecture.md).

![Figura de Hub e spoke ](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
 **: Wan virtual do Azure**

O modelo de conectividade Hub e spoke do Azure foi adotado por milhares de nossos clientes para aproveitar o comportamento de roteamento transitivo padrão da rede do Azure a fim de criar redes em nuvem simples e escalonáveis. A WAN virtual do Azure baseia-se nesses conceitos e apresenta novos recursos que permitem topologias de conectividade global, não apenas entre locais e o Azure, mas também permite que os clientes aproveitem a escala da rede da Microsoft para ampliar suas redes globais existentes.

Este artigo mostra como migrar um ambiente híbrido existente para a WAN virtual.

## <a name="scenario"></a>Cenário

A contoso é uma organização financeira global com escritórios na Europa e na Ásia. Eles estão planejando mover seus aplicativos existentes de um data center local no Azure e criar um design básico com base na arquitetura Hub e spoke manual, incluindo redes virtuais de Hub gerenciadas pelo cliente regionais para conectividade híbrida. Como parte da mudança para as tecnologias baseadas em nuvem, a equipe de rede tem sido tarefa garantindo que a conectividade seja otimizada para que a empresa avance.

A figura a seguir mostra uma exibição de alto nível da rede global existente, incluindo a conectividade a várias regiões do Azure.

![Topologia de rede existente da Contoso ](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
 **Figura: topologia de rede existente da Contoso**

Os pontos seguintes podem ser compreendidos da topologia de rede existente:

- Uma topologia hub e spoke é usada em várias regiões, incluindo circuitos Premium do ExpressRoute para conectividade de volta para uma WAN privada comum.

- Alguns desses sites também têm túneis VPN ligados diretamente ao Azure para alcançar aplicativos hospedados dentro da nuvem Microsoft.

## <a name="requirements"></a>Requisitos

A equipe de rede ficou encarregada de fornecer um modelo de rede global capaz de dar suporte à migração da Contoso para a nuvem e otimizar as áreas de custo, escala e desempenho. Em resumo, os seguintes requisitos devem ser atendidos:

- Fornecer à sede social (HQ) e às branches um caminho otimizado para aplicativos hospedados na nuvem.
- Remova a dependência em data centers locais existentes (DC) para terminação de VPN enquanto mantém os seguintes caminhos de conectividade:
  - **Ramificação para VNet**: escritórios conectados de VPN devem ser capazes de acessar aplicativos migrados para a nuvem na região local do Azure.
  - **Branch para Hub para VNet**: os escritórios conectados de VPN devem ser capazes de acessar aplicativos migrados para a nuvem na região remota do Azure.
  - **Ramificação para ramificação**: escritórios regionais conectados à VPN devem ser capazes de se comunicar entre si e sites de HQ/DC conectados do ExpressRoute.
  - **Ramificação para Hub para ramificação**: os escritórios conectados de VPN separados globalmente devem ser capazes de se comunicar entre si e quaisquer sites de HQ/DC conectados ao ExpressRoute.
  - **Ramificação para a Internet**: os sites conectados devem ser capazes de se comunicar com a Internet. Esse tráfego deve ser filtrado e registrado.
  - **Vnet para vnet**: as redes virtuais spoke na mesma região devem ser capazes de se comunicar entre si.
  - **Vnet para Hub**para rede virtual: as redes virtuais spoke nas diferentes regiões devem ser capazes de se comunicar umas com as outras.
- Fornecer a capacidade de usuários móveis da Contoso (laptop e telefone) acessarem os recursos da empresa enquanto não estiverem na rede corporativa.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Arquitetura de WAN virtual do Azure

A figura a seguir mostra uma exibição de alto nível da topologia de destino atualizada usando a WAN virtual do Azure para atender aos requisitos detalhados na seção anterior.

![Arquitetura de WAN virtual Contoso ](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
 **Figura: arquitetura Wan virtual do Azure**

Resumo:

- a HQ na Europa permanece conectada ao ExpressRoute, os DCs locais da Europa são totalmente migrados ao Azure e agora desativados.
- Os DCs e a HQ da Ásia permanecem conectados à WAN Particular. A WAN virtual do Azure agora é usada para aumentar a rede da operadora local e fornecer conectividade global.
- Os hubs de WAN virtual do Azure implantados nas regiões Europa Ocidental e Sul Ásia Oriental Azure para fornecer o Hub de conectividade para dispositivos conectados por VPN e ExpressRoute.
- Os hubs também fornecem a terminação de VPN para usuários móveis em vários tipos de clientes, usando a conectividade OpenVPN com a rede da malha global, permitindo acesso não apenas aos aplicativos migrados para o Azure, mas também a todos os recursos restantes no local.
- Conectividade com a Internet para recursos dentro de uma rede virtual fornecida pela WAN Virtual do Azure.

Conectividade com a Internet para sites remoto também fornecida pela WAN Virtual do Azure. Análise de Internet local com suporte por meio da integração de parceiro para acesso otimizado a serviços SaaS, como o Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrar para a WAN Virtual

Esta seção mostra as várias etapas para migrar para a WAN virtual do Azure.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Etapa 1: Hub gerenciado pelo cliente de região única e spoke

A figura a seguir mostra uma topologia de região única para contoso antes da distribuição da WAN virtual do Azure:

![Topologia de região única ](./media/migrate-from-hub-spoke-topology/figure1.png)
 **Figura 1: Hub manual de região única-e-spoke**

Ao manter a abordagem de Hub e spoke, a rede virtual de Hub gerenciada pelo cliente contém vários blocos de função:

- Serviços compartilhados (qualquer função comum exigida por vários spokes). Exemplo: a contoso usa controladores de domínio do Windows Server em máquinas virtuais IaaS (infraestrutura como um serviço).
- Os serviços de firewall de IP/roteamento são fornecidos por uma solução de virtualização de rede de terceiros que permite o roteamento de IP de camada 3 spoke para spoke.
- Serviços de entrada/saída da Internet, incluindo o Gateway de Aplicativo do Azure para solicitações HTTPS de entrada e serviços de proxy de terceiros em execução em máquinas virtuais para acesso de saída filtrado aos recursos da Internet.
- Gateway de rede virtual de ExpressRoute e VPN para conectividade com redes locais.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Etapa 2: implantar os hubs de WAN virtual

Implante um hub WAN virtual em cada região. Configure o Hub WAN virtual com gateway de VPN e gateway de ExpressRoute, conforme descrito nos seguintes artigos:

- [Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure](virtual-wan-site-to-site-portal.md)
- [Tutorial: criar uma associação de ExpressRoute usando a WAN virtual do Azure](virtual-wan-expressroute-portal.md)

> [!NOTE]
> A WAN virtual do Azure deve estar usando o SKU Standard para habilitar alguns dos caminhos de tráfego mostrados neste artigo.

![Implantar hubs de WAN virtual ](./media/migrate-from-hub-spoke-topology/figure2.png)
 **Figura 2: Hub gerenciado pelo cliente-e-spoke para migração de WAN virtual**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Etapa 3: conectar sites remotos (ExpressRoute e VPN) à WAN virtual

Conecte o Hub WAN virtual aos circuitos do ExpressRoute existentes e configure VPNs site a site pela Internet para qualquer Branch remoto.

> [!NOTE]
> Os circuitos do ExpressRoute devem ser atualizados para o tipo de SKU Premium para se conectarem ao hub da WAN Virtual.

![Conectar sites remotos à WAN virtual ](./media/migrate-from-hub-spoke-topology/figure3.png)
 **Figura 3: Hub gerenciado pelo cliente e spoke para migração de WAN virtual**

Neste ponto, o equipamento de rede local começará a receber rotas que refletem o espaço de endereço IP atribuído à VNet do Hub gerenciado pela WAN virtual. As filiais conectadas à VPN remota nesta fase verão dois caminhos para todos os aplicativos existentes nas redes virtuais spoke. Esses dispositivos devem ser configurados para continuar a usar o túnel para o Hub gerenciado pelo cliente para garantir o roteamento simétrico durante a fase de transição.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Etapa 4: testar a conectividade híbrida via WAN virtual

Antes de usar o Hub de WAN virtual gerenciado para conectividade de produção, recomendamos que você configure uma rede virtual de spoke de teste e uma conexão VNet de WAN virtual. Valide se as conexões com esse ambiente de teste funcionam por meio do ExpressRoute e da VPN site a site antes de continuar com as próximas etapas.

![Testar a conectividade híbrida via WAN virtual ](./media/migrate-from-hub-spoke-topology/figure4.png)
 **Figura 4: Hub gerenciado pelo cliente e spoke para migração de WAN virtual**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Etapa 5: transição de conectividade para o Hub de WAN virtual

![Conectividade de transição para o Hub de WAN virtual ](./media/migrate-from-hub-spoke-topology/figure5.png)
 **Figura 5: Hub gerenciado pelo cliente e spoke para migração de WAN virtual**

**a**. Exclua as conexões de emparelhamento existentes de redes virtuais spoke para o Hub gerenciado pelo cliente antigo. O acesso aos aplicativos nas redes virtuais spoke fica indisponível até que as etapas de a-c sejam concluídas.

**b**. Conecte as redes virtuais spoke ao Hub WAN virtual por meio de conexões VNet.

**c**. Remova todas as UDR (rotas definidas pelo usuário) usadas anteriormente em redes virtuais spoke para comunicações spoke a spoke. Agora esse caminho está habilitado pelo roteamento dinâmico disponível no hub da WAN Virtual.

**d**. Gateways de VPN e ExpressRoute existentes no Hub gerenciado pelo cliente agora são descomissionados para permitir a próxima etapa (e).

**e**. Conecte o Hub gerenciado pelo cliente antigo (rede virtual do Hub) ao Hub WAN virtual por meio de uma nova conexão de VNet.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Etapa 6: Hub antigo torna-se serviços compartilhados spoke

Agora, reprojetamos nossa rede do Azure para tornar o Hub da WAN Virtual o ponto central em nossa nova topologia.

![Hub antigo se torna serviços compartilhados spoke ](./media/migrate-from-hub-spoke-topology/figure6.png)
 **Figura 6: Hub gerenciado pelo cliente-e-spoke para migração de WAN virtual**

Como o Hub de WAN virtual é uma entidade gerenciada e não permite a implantação de recursos personalizados, como máquinas virtuais, o bloco de serviços compartilhados agora existe como uma rede virtual spoke e hospeda funções como entrada de Internet por meio de Aplicativo Azure gateway ou dispositivo de rede virtualizado. O tráfego entre o ambiente de serviços compartilhados e as máquinas virtuais de back-end agora transita o hub gerenciado da WAN Virtual.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Etapa 7: otimizar a conectividade local para utilizar totalmente a WAN virtual

Nesta fase, a Contoso já concluiu a maior parte de suas migrações de aplicativos de negócios para dentro do Microsoft Cloud, restando apenas alguns aplicativos herdados dentro do DC local.

![Otimize a conectividade local para utilizar totalmente a WAN virtual ](./media/migrate-from-hub-spoke-topology/figure7.png)
 **Figura 7: Hub gerenciado pelo cliente e spoke para migração de WAN virtual**

Para aproveitar a funcionalidade completa da WAN Virtual do Azure, a Contoso decide desativar suas conexões VPN local herdadas. Qualquer branch que continuar a acessar as redes da HQ ou do DC será capaz de transitar a rede global da Microsoft usando o roteamento de trânsito interno da WAN Virtual do Azure.

> [!NOTE]
> O Alcance Global do ExpressRoute é uma opção alternativa para os clientes que desejam aproveitar o backbone Microsoft para complementar suas WANs particulares existentes.

## <a name="end-state-architecture-and-traffic-paths"></a>Arquitetura de estado final e caminhos de tráfego

![Arquitetura de estado final e caminhos de tráfego ](./media/migrate-from-hub-spoke-topology/figure8.png)
 **Figura: Wan virtual de região dupla**

Esta seção fornece um resumo de como essa topologia atende aos requisitos originais examinando alguns fluxos de tráfego de exemplo.

### <a name="path-1"></a>Caminho 1

O caminho 1 mostra o fluxo de tráfego de um Branch conectado de VPN S2S na Ásia para uma VNet do Azure na região do Sul Ásia Oriental.

O tráfego é roteado da seguinte maneira:

- A ramificação da Ásia é conectada por meio de túneis com BGP S2S habilitados para o Sul Ásia Oriental Hub de WAN virtual.

- O hub da WAN Virtual da Ásia roteia o tráfego localmente para a VNet conectada.

![Fluxo 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Caminho 2

O caminho 2 mostra o fluxo de tráfego da sede Europeia conectada do ExpressRoute para uma VNet do Azure na região do Sul Ásia Oriental.

O tráfego é roteado da seguinte maneira:

- A sede Europeia está conectada por meio do circuito do ExpressRoute Premium em Europa Ocidental Hub de WAN virtual.

- A conectividade global de Hub para Hub da WAN Virtual permite o trânsito de tráfego para a VNet conectada na região remota.

![Fluxo 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Caminho 3

O caminho 3 mostra o fluxo de tráfego do DC local da Ásia conectado à WAN privada para um Branch conectado do S2S Europeu.

O tráfego é roteado da seguinte maneira:

- o DC da Ásia está conectado à operadora de WAN Particular local.

- O circuito do ExpressRoute é encerrado localmente na WAN privada conecta-se ao Sul Ásia Oriental Hub de WAN virtual.

- A conectividade global de Hub para Hub de WAN virtual permite o trânsito do tráfego.

![Fluxo 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Caminho 4

O caminho 4 mostra o fluxo de tráfego de uma VNet do Azure na região do Sul Ásia Oriental para uma VNet do Azure na região Europa Ocidental.

O tráfego é roteado da seguinte maneira:

- A conectividade global hub a Hub da WAN Virtual permite o trânsito nativo de todas as VNets do Azure conectadas sem configurações adicionais do usuário.

![Fluxo 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Caminho 5

O caminho 5 mostra o fluxo de tráfego de usuários de VPN de roaming (P2S) para uma VNet do Azure na região de Europa Ocidental.

O tráfego é roteado da seguinte maneira:

- Os usuários de laptop e dispositivo móvel usam o cliente OpenVPN para conectividade transparente no gateway de VPN P2S no Europa Ocidental.

- O hub da WAN Virtual do Oeste da Europa roteia o tráfego localmente para a VNet conectada.

![Fluxo 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Controle de segurança e políticas por meio do Firewall do Azure

A contoso agora validou a conectividade entre todas as ramificações e VNets em linha com os requisitos discutidos anteriormente neste artigo. Para atender aos requisitos de controle de segurança e isolamento de rede, eles precisam continuar a separar e registrar o tráfego por meio da rede de Hub. Anteriormente, essa função foi executada por uma solução de virtualização de rede (NVA). A contoso também deseja encerrar seus serviços de proxy existentes e utilizar os serviços nativos do Azure para a filtragem de saída da Internet.

![Segurança e controle de política por meio do firewall do Azure ](./media/migrate-from-hub-spoke-topology/security-policy.png)
 **Figura: Firewall do Azure na WAN virtual (Hub virtual protegido)**

As etapas de alto nível a seguir são necessárias para introduzir o Firewall do Azure nos hubs de WAN virtual para habilitar um ponto unificado de controle de política. Para obter mais informações sobre esse processo e o conceito de hubs virtuais seguros, consulte [Gerenciador de firewall do Azure](../firewall-manager/index.yml).

1. Crie uma Política de Firewall do Azure.
2. Vincule uma política de firewall ao hub da WAN Virtual do Azure. Esta etapa permite que o Hub WAN virtual existente funcione como um hub virtual seguro e implanta os recursos de firewall do Azure necessários.

> [!NOTE]
> Se o Firewall do Azure for implantado em um hub de WAN virtual padrão (SKU: Standard): as políticas do V2V, B2V, V2I e B2I FW serão impostas apenas no tráfego originário do VNets e branches conectados ao Hub específico em que o FW do Azure é implantado (Hub protegido). O tráfego proveniente de VNets remotos e branches que estão anexados a outros hubs de WAN virtuais na mesma WAN virtual não será "firewall", mesmo que os branches remotos e VNet sejam interconectados por meio do hub de WAN virtual a links de Hub. O suporte a firewalls entre hubs está no roteiro de WAN virtual e do Gerenciador de firewall do Azure.

Os caminhos a seguir mostram os caminhos de conectividade habilitados usando os hubs virtuais protegidos do Azure:

### <a name="path-6"></a>Caminho 6

O caminho 6 mostra o fluxo de tráfego seguro entre VNets na mesma região.

O tráfego é roteado da seguinte maneira:

- as Redes Virtuais conectadas ao mesmo Hub Virtual Seguro agora roteiam o tráfego por meio do Firewall do Azure.

- O Firewall do Azure pode aplicar políticas a esses fluxos.

![Fluxo 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Caminho 7

O caminho 7 mostra o fluxo de tráfego de uma VNet do Azure para a Internet ou serviço de segurança de terceiros.

O tráfego é roteado da seguinte maneira:

- as Redes Virtuais conectadas ao Hub Virtual Seguro podem enviar tráfego para destinos públicos na Internet, usando o Hub Seguro como um ponto central de acesso à Internet.

- Esse tráfego pode ser filtrado localmente usando as regras de FQDN do firewall do Azure ou enviado a um serviço de segurança de terceiros para inspeção.

![Fluxo 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Caminho 8

O caminho 8 mostra o fluxo de tráfego do serviço de segurança da filial para a Internet ou de terceiros.

O tráfego é roteado da seguinte maneira:

- As ramificações conectadas ao Secure virtual Hub podem enviar tráfego para destinos públicos na Internet usando o Secure Hub como um ponto central de acesso à Internet.

- Esse tráfego pode ser filtrado localmente usando as regras de FQDN do firewall do Azure ou enviado a um serviço de segurança de terceiros para inspeção.

![Fluxo 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [WAN Virtual do Azure](virtual-wan-about.md)
