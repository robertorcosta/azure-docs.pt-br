---
title: 'Configurar criptografia ExpressRoute: IPsec over ExpressRoute for Azure Virtual WAN'
description: Neste tutorial, aprenda a usar o Azure Virtual WAN para criar uma conexão VPN site-to-site através do peering privado ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059298"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Criptografia ExpressRoute: IPsec sobre ExpressRoute para WAN Virtual

Este artigo mostra como usar o Azure Virtual WAN para estabelecer uma conexão IPsec/IKE VPN da sua rede local para o Azure sobre o peering privado de um circuito Azure ExpressRoute. Essa técnica pode fornecer um trânsito criptografado entre as redes locais e as redes virtuais do Azure via ExpressRoute, sem passar pela internet pública ou usar endereços IP públicos.

## <a name="topology-and-routing"></a>Topologia e roteamento

O diagrama a seguir mostra um exemplo de conectividade VPN sobre peering privado ExpressRoute:

![VPN sobre ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

O diagrama mostra uma rede dentro da rede local conectada ao gateway VPN do hub DoZure sobre o peering privado ExpressRoute. O estabelecimento de conectividade é simples:

1. Estabeleça a conectividade ExpressRoute com um circuito ExpressRoute e peering privado.
2. Estabeleça a conectividade VPN conforme descrito neste artigo.

Um aspecto importante dessa configuração é o roteamento entre as redes locais e o Azure sobre os caminhos ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego de redes locais para Azure

Para tráfego de redes locais para o Azure, os prefixos do Azure (incluindo o hub virtual e todas as redes virtuais de fala conectadas ao hub) são anunciados tanto através do BGP privado ExpressRoute quanto do BGP VPN. Isso resulta em duas rotas de rede (caminhos) em direção ao Azure a partir das redes locais:

- Um sobre o caminho protegido pelo IPsec
- Um diretamente sobre ExpressRoute *sem* proteção IPsec 

Para aplicar a criptografia à comunicação, você deve ter certeza de que, para a rede conectada à VPN no diagrama, as rotas do Azure através do gateway VPN no local são preferidas sobre o caminho direto do ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego do Azure para redes locais

O mesmo requisito se aplica ao tráfego do Azure para redes locais. Para garantir que o caminho IPsec seja preferido em relação ao caminho direct Do ExpressRoute (sem IPsec), você tem duas opções:

- Anuncie prefixos mais específicos na sessão VPN BGP para a rede conectada à VPN. Você pode anunciar uma gama maior que engloba a rede conectada à VPN através do peering privado ExpressRoute e, em seguida, faixas mais específicas na sessão VPN BGP. Por exemplo, anuncie 10.0.0.0/16 no ExpressRoute e 10.0.1.0/24 sobre VPN.

- Anuncie prefixos desarticulados para VPN e ExpressRoute. Se as faixas de rede conectadas à VPN forem desconexas de outras redes conectadas ao ExpressRoute, você poderá anunciar os prefixos nas sessões de Vpn e ExpressRoute BGP, respectivamente. Por exemplo, anuncie 10.0.0.0/24 no ExpressRoute e 10.0.1.0/24 sobre VPN.

Em ambos os exemplos, o Azure enviará tráfego para 10.0.1.0/24 através da conexão VPN, em vez de diretamente pelo ExpressRoute sem proteção VPN.

> [!WARNING]
> Se você anunciar os *mesmos* prefixos em ambas as conexões ExpressRoute e VPN, o Azure usará o caminho ExpressRoute diretamente sem proteção VPN.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Crie uma WAN virtual e um hub com gateways

Os seguintes recursos do Azure e as configurações on-premises correspondentes devem estar em vigor antes de prosseguir:

- Um WAN virtual do Azure
- Um hub WAN virtual com um [gateway ExpressRoute](virtual-wan-expressroute-portal.md) e um [gateway VPN](virtual-wan-site-to-site-portal.md)

Para obter as etapas para criar uma WAN virtual do Azure e um hub com uma associação ExpressRoute, consulte [Criar uma associação ExpressRoute usando o Azure Virtual WAN](virtual-wan-expressroute-portal.md). Para obter as etapas para criar um gateway VPN na WAN virtual, consulte [Criar uma conexão site-a-site usando o Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Crie um site para a rede local

O recurso do site é o mesmo que os sites vpn não ExpressRoute para uma WAN virtual. O endereço IP do dispositivo VPN local agora pode ser um endereço IP privado ou um endereço IP público na rede local acessível via peering privado ExpressRoute criado na etapa 1.

> [!NOTE]
> O endereço IP do dispositivo VPN local *deve* fazer parte dos prefixos de endereço anunciados para o hub WAN virtual via peering privado Do Azure ExpressRoute.
>

1. Acesse o portal Azure no seu navegador. 
1. Selecione o WAN que você criou. Na página WAN, em **Conectividade,** selecione **sites VPN.**
1. Na página de **sites vpn,** selecione **+Criar site**.
1. Na página **Criar site**, preencha os seguintes campos:
   * **Assinatura**: Verifique a assinatura.
   * **Grupo de recursos**: Selecione ou crie o grupo de recursos que deseja usar.
   * **Região**: Digite a região do Azure para obter o recurso do site VPN.
   * **Nome**: Digite o nome pelo qual deseja consultar o site no local.
   * **Fornecedor de dispositivos**: Digite o fornecedor do dispositivo VPN local.
   * **Protocolo do Gateway de Fronteira**: Selecione "Ativar" se a rede local usar BGP.
   * **Espaço de endereço privado**: Digite o espaço de endereço IP que está localizado no seu site no local. O tráfego destinado a este espaço de endereço é encaminhado para a rede local através do gateway VPN.
   * **Hubs**: Selecione um ou mais hubs para conectar este site vpn. Os hubs selecionados devem ter gateways VPN já criados.
1. Selecione **A seguir: Links >** para as configurações do link VPN:
   * **Nome do link**: O nome pelo qual você deseja se referir a esta conexão.
   * **Nome do provedor**: O nome do provedor de serviços de internet para este site. Para uma rede local ExpressRoute, é o nome do provedor de serviços ExpressRoute.
   * **Velocidade**: A velocidade do link de serviço de internet ou circuito ExpressRoute.
   * **Endereço IP**: O endereço IP público do dispositivo VPN que reside no seu site local. Ou, para o ExpressRoute no local, é o endereço IP privado do dispositivo VPN via ExpressRoute.

   Se o BGP estiver habilitado, ele se aplicará a todas as conexões criadas para este site no Azure. Configurar o BGP em uma WAN virtual equivale à configuração do BGP em um gateway Azure VPN. 
   
   Seu endereço bgp no local *não deve* ser o mesmo que o endereço IP da sua VPN para o dispositivo ou o espaço de endereço de rede virtual do site VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, *não pode* ser uma APIPA (169.254).* x*. *x*) endereço. Especifique este endereço no gateway de rede local correspondente que representa o local. Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selecione **A seguir: Revise + crie >** para verificar os valores de configuração e criar o site vpn. Se você selecionou **hubs** para se conectar, a conexão será estabelecida entre a rede local e o gateway VPN do hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Atualize a configuração de conexão VPN para usar o ExpressRoute

Depois de criar o site vpn e se conectar ao hub, use as seguintes etapas para configurar a conexão para usar o peering privado ExpressRoute:

1. Volte para a página de recursos de WAN virtual e selecione o recurso do hub. Ou navegar do site VPN para o hub conectado.
1. Em **Conectividade,** selecione **VPN (site-to-site)**.
1. Selecione a elipse **( ...**) no site vpn via ExpressRoute e selecione **Editar conexão VPN para este hub**.
1. Para **usar o endereço IP privado do Azure,** selecione **Sim**. A configuração configura o gateway VPN do hub para usar endereços IP privados dentro do intervalo de endereços do hub no gateway para essa conexão, em vez dos endereços IP públicos. Isso garantirá que o tráfego da rede local atravesse os caminhos privados de peering do ExpressRoute em vez de usar a internet pública para essa conexão VPN. A captura de tela a seguir mostra a configuração.

   ![Configuração para usar um endereço IP privado para a conexão VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Selecione **Salvar**.

Depois de salvar suas alterações, o gateway VPN do hub usará os endereços IP privados no gateway VPN para estabelecer as conexões IPsec/IKE com o dispositivo VPN local via ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Obtenha os endereços IP privados para o gateway VPN do hub

Baixe a configuração do dispositivo VPN para obter os endereços IP privados do gateway VPN do hub. Você precisa desses endereços para configurar o dispositivo VPN no local.

1. Na página do seu hub, selecione **VPN (Site-to-Site)** em **Conectividade**.
1. Na parte superior da página **Visão Geral,** selecione **Baixar VPN Config**. 

   O Azure cria uma conta de armazenamento no grupo de recursos "microsoft-network-[location]", onde a *localização* é a localização da WAN. Depois de aplicar a configuração aos seus dispositivos VPN, você pode excluir essa conta de armazenamento.
1. Depois que o arquivo for criado, selecione o link para baixá-lo.
1. Aplique a configuração ao dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>Arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usados quando você estiver configurando seu dispositivo VPN no local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration**: Esta seção denota os detalhes do dispositivo configurados como um site que está se conectando à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da filial.
* **vpnConexões site**: Esta seção fornece informações sobre as seguintes configurações:

    * Dirija-se ao espaço da rede virtual do hub virtual.<br/>Exemplo:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Dirija-se ao espaço das redes virtuais que estão conectadas ao hub.<br>Exemplo:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Endereços IP do gateway VPN do hub virtual. Como cada conexão do gateway VPN é composta por dois túneis na configuração ativa, você verá ambos os endereços IP listados neste arquivo. Neste exemplo, você `Instance0` `Instance1` vê e para cada site, e eles são endereços IP privados em vez de endereços IP públicos.<br>Exemplo:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Detalhes de configuração para a conexão do gateway VPN, como BGP e chave pré-compartilhada. A chave pré-compartilhada é gerada automaticamente para você. Você sempre pode editar a conexão na página **Visão Geral** para uma chave pré-compartilhada personalizada.
  
### <a name="example-device-configuration-file"></a>Exemplo de arquivo de configuração de dispositivo

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

Se você precisar de instruções para configurar o dispositivo, poderá usar as instruções sobre a [página de scripts de configuração de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) com as seguintes condições:

* As instruções na página do dispositivo VPN não são escritas para uma WAN virtual. Mas você pode usar os valores de WAN virtuais do arquivo de configuração para configurar manualmente seu dispositivo VPN. 
* Os scripts de configuração de dispositivo para download que são para o gateway VPN não funcionam para a WAN virtual, porque a configuração é diferente.
* Uma nova WAN virtual pode suportar tanto ikev1 quanto IKEv2.
* Uma WAN virtual pode usar apenas dispositivos VPN baseados em rota e instruções de dispositivo.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Veja sua WAN virtual

1. Vá para o WAN virtual.
1. Na página **Visão Geral**, cada ponto no mapa representa um hub.
1. Na seção **Hubs e conexões,** você pode visualizar o status de conexão hub, site, região e VPN. Você também pode ver bytes dentro e fora.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Monitore uma conexão

Crie uma conexão para monitorar a comunicação entre uma máquina virtual Azure (VM) e um local remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP vm no Azure, e o IP de destino é o IP do site.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Limpar recursos

Quando você não precisar mais desses recursos, você pode usar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Execute o seguinte comando PowerShell e substitua com `myResourceGroup` o nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Este artigo ajuda você a criar uma conexão VPN através do peering privado ExpressRoute usando WAN virtual. Para saber mais sobre a WAN virtual e os recursos relacionados, consulte a visão geral da [WAN virtual](virtual-wan-about.md).
