---
title: Conecte um gateway de rede virtual a um WAN virtual do Azure
description: Este artigo ajuda você a conectar um gateway de rede virtual do Azure a um gateway Azure Virtual WAN VPN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066244"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Conecte um Gateway VPN (gateway de rede virtual) ao WAN virtual

Este artigo ajuda você a configurar a conectividade a partir de um Gateway de VPN do Azure VPN (gateway de rede virtual) para um Azure Virtual WAN (gateway VPN). Criar uma conexão a partir de um GATEWAY VPN (gateway de rede virtual) para um WAN virtual (gateway VPN) é semelhante à configuração de conectividade a uma WAN virtual a partir de sites de VPN de ramificação.

A fim de minimizar uma possível confusão entre dois recursos, prefáciaremos o gateway com o nome do recurso a que estamos nos referindo. Por exemplo, o gateway de rede virtual VPN Gateway e o gateway VIRTUAL WAN VPN.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, crie os seguintes recursos:

WAN Virtual do Azure

* [Crie uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan).
* [Crie um hub](virtual-wan-site-to-site-portal.md#hub). O hub virtual contém o gateway VIRTUAL WAN VPN.

Rede Virtual do Azure

* Crie uma rede virtual sem gateways de rede virtuais. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Crie um gateway de rede virtual do Azure

Crie um gateway de rede virtual VPN Gateway para sua rede virtual no modo ativo para sua rede virtual. Quando você cria o gateway, você pode usar endereços IP públicos existentes para as duas instâncias do gateway ou pode criar novos IPs públicos. Você usa esses IPs públicos ao configurar os sites de WAN virtual. Para obter mais informações sobre o modo ativo, consulte [Configurar conexões ativas](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Configuração do modo ativo ativo

![ativo-ativo](./media/connect-virtual-network-gateway-vwan/active.png "ativo-ativo")

### <a name="bgp-setting"></a><a name="BGP"></a>Configuração BGP

O BGP ASN não pode ser 65515. 66515 será usado pelo Azure Virtual WAN.

![Bgp](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Endereços IP públicos

Quando o gateway for criado, navegue até a página **Propriedades.** As propriedades e configurações serão semelhantes ao exemplo a seguir. Observe os dois endereços IP públicos que são usados para o gateway.

![Propriedades](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Crie sites virtuais de VPN WAN

Para criar sites de VPN WAN virtuais, navegue até sua WAN virtual e, em **Conectividade,** selecione **sites VPN**. Nesta seção, você criará dois sites de VPN WAN virtuais que correspondem aos gateways de rede virtuais criados na seção anterior.

1. Selecione **+Criar site**.
2. Na página **Criar sites VPN,** digite os seguintes valores:

   * **Região** - (A mesma região do gateway de rede virtual Azure VPN Gateway)
   * **Fornecedor de dispositivos** - Digite o fornecedor do dispositivo (qualquer nome)
   * **Espaço de endereço privado** - (Digite um valor ou deixe em branco quando o BGP estiver ativado)
   * **Border Gateway Protocol** - (Definido para **ativar** se o gateway de rede virtual do Azure VPN Gateway tiver o BGP ativado)
   * **Conecte-se a hubs** (Selecione o hub que você criou nos pré-requisitos a partir da estada)
3. Em **Links,** insira os seguintes valores:

   * **Nome do provedor** - Digite um nome de link e um nome do provedor (qualquer nome)
   * **Velocidade** - Velocidade (qualquer número)
   * **Endereço IP** - Digite endereço IP (o mesmo que o primeiro endereço IP público mostrado as propriedades do gateway de rede virtual (VPN Gateway)
   * **Endereço BGP** e **ASN** - Endereço BGP e ASN. Estes devem ser os mesmos que um dos endereços IP de peer BGP e ASN do gateway de rede virtual VPN Gateway que você configurou na [Etapa 1](#vnetgw).
4. Revise e **selecione Confirmar** para criar o site.
5. Repita as etapas anteriores para criar o segundo site para combinar com a segunda instância do gateway de rede virtual VPN Gateway. Você manterá as mesmas configurações, exceto usando o segundo endereço IP público e o segundo endereço IP do ponto BGP da configuração do VPN Gateway.
6. Agora você tem dois sites provisionados com sucesso e pode prosseguir para a próxima seção para baixar arquivos de configuração.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Baixe os arquivos de configuração VPN

Nesta seção, você baixa o arquivo de configuração VPN para cada um dos sites que você criou na seção anterior.

1. Na parte superior da página dos sites da WAN **VPN** virtual, selecione o **Site**e selecione **Baixar configuração VPN site-to-site**. O Azure cria um arquivo de configuração com as configurações.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/download.png "download")
2. Baixe e abra o arquivo de configuração.
3. Repita estas etapas para o segundo local. Uma vez que ambos os arquivos de configuração abertos, você pode prosseguir para a próxima seção.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Crie os gateways de rede locais

Nesta seção, você cria dois gateways de rede locais do Azure VPN Gateway. Os arquivos de configuração da etapa anterior contêm as configurações de configuração do gateway. Use essas configurações para criar e configurar os gateways de rede locais do Azure VPN Gateway.

1. Crie o gateway de rede local usando essas configurações. Para obter informações sobre como criar um gateway de rede local do VPN Gateway, consulte o artigo do VPN Gateway [Criar um gateway de rede local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Endereço IP** - Use o endereço IP Instance0 mostrado para *configuração* de gateway a partir do arquivo de configuração.
   * **BGP** - Se a conexão estiver sobre BGP, **selecione Configurar configurações BGP** e digite o ASN '65515'. Digite o endereço IP do peer BGP. Use 'Instance0 BgpPeeringAddresses' para *configuração* de gateway a partir do arquivo de configuração.
   * **Assinatura, grupo de recursos e localização** são os mesmos do hub WAN virtual.
2. Revise e crie o gateway de rede local. Seu gateway de rede local deve ser semelhante a este exemplo.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/lng1.png "exemplo0")
3. Repita essas etapas para criar outro gateway de rede local, mas, desta vez, use os valores 'Instance1' em vez de 'Instance0' do arquivo de configuração.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/lng2.png "instância1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Crie conexões

Nesta seção, você cria uma conexão entre os gateways de rede locais do VPN Gateway e o gateway de rede virtual. Para obter etapas sobre como criar uma conexão VPN Gateway, consulte [Configurar uma conexão](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. No portal, navegue até o portal de rede virtual e clique **em Conexões**. Na parte superior da página Conexões, clique em **+ Adicionar** para abrir a página **Adicionar conexão**.
2. Na página **Adicionar conexão,** configure os seguintes valores para sua conexão:

   * **Nome:** nomeie sua conexão.
   * **Tipo de conexão:** Selecione **IPSec (Site-to-site)**
   * **Gateway de rede virtual:** o valor é fixo porque você está se conectando por esse gateway.
   * **Gateway de rede local:** Essa conexão conectará o gateway de rede virtual ao gateway de rede local. Escolha um dos gateways de rede locais que você criou anteriormente.
   * **Chave compartilhada:** Digite uma chave compartilhada.
   * **Protocolo IKE:** Escolha o protocolo IKE.
   * **BGP:** Escolha **Ativar BGP** se a conexão estiver acima do BGP.
3. Clique em **OK** para criar sua conexão.
4. Você pode exibir a conexão na página **Conexões** do seu gateway de rede virtual.

   ![Conexão](./media/connect-virtual-network-gateway-vwan/connect.png "conexão")
5. Repita as etapas anteriores para criar uma segunda conexão. Para a segunda conexão, selecione o outro gateway de rede local que você criou.

## <a name="6-test-connections"></a><a name="test"></a>6. Conexões de teste

Você pode testar a conectividade criando duas máquinas virtuais, uma na lateral do gateway de rede virtual VPN Gateway e outra em uma rede virtual para a WAN Virtual e, em seguida, pingando as duas máquinas virtuais.

1. Crie uma máquina virtual na rede virtual (Test1-VNet) para o Azure VPN Gateway (Test1-VNG). Não crie a máquina virtual na GatewaySubnet.
2. Crie outra rede virtual para se conectar à WAN virtual. Crie uma máquina virtual em uma sub-rede desta rede virtual. Esta rede virtual não pode conter nenhum gateway de rede virtual. Você pode criar rapidamente uma rede virtual usando as etapas do PowerShell no artigo [de conexão site-a-site.](virtual-wan-site-to-site-portal.md#vnet) Certifique-se de alterar os valores antes de executar os cmdlets.
3. Conecte o VNet ao hub WAN virtual. Na página para sua WAN virtual, selecione **Conexões de rede Virtual**e **+Adicione conexão**. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de rede virtual.
5. A conectividade agora está definida entre as VMs. Você deve ser capaz de pingar uma VM do outro, a menos que haja quaisquer firewalls ou outras políticas bloqueando a comunicação.

## <a name="next-steps"></a>Próximas etapas

Para obter etapas para configurar uma diretiva IPsec personalizada, consulte [Configurar uma diretiva IPsec personalizada para WAN virtual](virtual-wan-custom-ipsec-portal.md).
Para obter mais informações sobre WAN Virtual, consulte [Sobre a WAN Virtual do Azure](virtual-wan-about.md) e [Perguntas frequentes sobre a WAN Virtual do Azure](virtual-wan-faq.md).