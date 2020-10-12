---
title: 'Conectar a rede local à rede virtual do Azure: VPN site a site: Portal'
description: Crie uma conexão de gateway de VPN site a site IPsec de sua rede local para uma rede virtual do Azure pela Internet pública usando o Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 660c346669c684921164f6488647f589492c7416
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006611"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Criar uma conexão Site a Site no portal do Azure

Este artigo mostra como usar o portal do Azure para criar uma conexão de gateway de VPN Site a Site de sua rede local para a rede virtual. As etapas neste artigo se aplicam ao modelo de implantação do Resource Manager. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre os gateways de VPN, veja [Sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).

![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Verifique se você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Para obter mais informações sobre dispositivos VPN compatíveis e a configuração de dispositivo, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes. Ao criar essa configuração, você deve especificar os prefixos de intervalo de endereços IP que o Azure roteará para seu local. Nenhuma das sub-redes da rede local podem se sobrepor às sub-redes de rede virtual às quais você deseja se conectar. 

### <a name="example-values"></a><a name="values"></a>Valores de exemplo

Os exemplos neste artigo usam os seguintes valores. Você pode usar esses valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo. Para obter mais informações sobre configurações de Gateway de VPN em geral, confira [Sobre as configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

* **Nome da rede virtual:** VNet1
* **Espaço de endereço:** 10.1.0.0/16
* **Assinatura**: a assinatura que você quer usar
* **Grupo de recursos:** TestRG1
* **Região:** Leste dos EUA
* **Sub-rede**: Front-end: 10.1.0.0/24, Back-end: 10.1.1.0/24 (opcional para este exercício)
* **Intervalo de endereços da sub-rede do gateway:** 10.1.255.0/27
* **Nome do gateway de rede virtual:** VNet1GW
* **Endereço IP público:** VNet1GWpip
* **Tipo de VPN:** Baseado em rota
* **Tipo de conexão:** Site a site (IPsec)
* **Tipo de gateway:** VPNS
* **Nome do gateway de rede local:** Site1
* **Nome da conexão:** VNet1toSite1
* **Chave compartilhada:** para esse exemplo, usaremos abc123. Mas você pode usar o que for compatível com o hardware de VPN. O importante é que os valores correspondam em ambos os lados da conexão.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. criar uma rede virtual

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-the-vpn-gateway"></a><a name="VNetGateway"></a>2. criar o gateway de VPN

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="example-settings"></a>Configurações de exemplo

* **Detalhes da instância > região:** Leste dos EUA
* Redes virtuais **> rede virtual:** VNet1
* **Detalhes da instância > nome:** VNet1GW
* **Detalhes da instância > tipo de gateway:** VPNS
* **Detalhes da instância > tipo de VPN:** Baseado em rota
* **Intervalo de endereços de sub-rede do gateway de > de rede virtual:** 10.1.255.0/27
* **Endereço IP público > nome do endereço IP público:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]


## <a name="3-create-the-local-network-gateway"></a><a name="LocalNetworkGateway"></a>3. criar o gateway de rede local

O gateway de rede local é um objeto específico que representa seu local (o site) para fins de roteamento. Você atribui um nome ao site pelo qual o Azure pode fazer referência a ele e especifica o endereço IP do dispositivo VPN local para o qual você criará uma conexão. Você também pode especificar os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local. Se as alterações de rede local ou se você precisar alterar o endereço IP público para o dispositivo VPN, poderá atualizar facilmente os valores mais tarde.

**Valores de exemplo**

* **Nome:** Site1
* **Grupo de recursos:** TestRG1
* **Local:** Leste dos EUA


[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="4-configure-your-vpn-device"></a><a name="VPNDevice"></a>4. configurar seu dispositivo VPN

As conexões Site a Site para uma rede local exigem um dispositivo VPN. Nesta etapa, você deve configurar seu dispositivo VPN. Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
- O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway de VPN usando o portal do Azure, navegue até **gateways de rede virtual**e clique no nome do seu gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="5-create-the-vpn-connection"></a><a name="CreateConnection"></a>5. criar a conexão VPN

Crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="6-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>6. verificar a conexão VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Para conectar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Como redefinir um gateway de VPN

Redefinir um gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN site a site. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. Para obter as etapas, consulte [Redefinir um gateway de VPN](vpn-gateway-resetgw-classic.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Como alterar um SKU de gateway (redimensionar um gateway)

Para obter as etapas para alterar um SKU de gateway, consulte [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Como inserir uma conexão adicional para um gateway de VPN

Você pode inserir conexões adicionais desde que nenhum dos espaços de endereço se sobreponham entre conexões.

1. Para inserir uma conexão adicional, navegue até o gateway de VPN e clique em **Conexões** para abrir a página Conexões.
2. Clique em **+Adicionar** para inserir sua conexão. Ajuste o tipo de conexão para refletir cada rede virtual para rede virtual (se conectando a outro gateway de rede virtual) ou site a site.
3. Se você estiver se conectando usando o Site a site e ainda não criou um gateway de rede local para o site ao qual deseja se conectar, é possível criar um novo.
4. Especifique a chave compartilhada que deseja usar e depois clique em **OK** para criar a conexão.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter mais informações sobre túneis forçados, consulte [Sobre o túnel forçado](vpn-gateway-forced-tunneling-rm.md).
* Para obter informações sobre Conexões Altamente Disponíveis Ativo-Ativo, consulte [Conectividade Altamente Disponível entre locais e Rede Virtual para Rede Virtual](vpn-gateway-highlyavailable.md).
* Para obter mais informações sobre como limitar o tráfego de rede para recursos em uma rede virtual, consulte [Segurança de Rede](../virtual-network/security-overview.md).
* Para obter mais informações sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet, consulte [Roteamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
* Para saber mais sobre como criar uma conexão VPN site a site usando o modelo do Azure Resource Manager, consulte [Criar uma conexão VPN site a site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Para saber mais sobre como criar uma conexão VPN rede virtual para rede virtual usando o modelo do Azure Resource Manager, consulte [Implantar a replicação geográfica do HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
