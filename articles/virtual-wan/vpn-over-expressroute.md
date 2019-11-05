---
title: Criar uma conexão VPN site a site por meio do emparelhamento privado do ExpressRoute na WAN virtual do Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN virtual do Azure para criar uma conexão VPN site a site por meio do emparelhamento privado do ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515025"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Criar uma conexão VPN site a site por meio do emparelhamento privado do ExpressRoute usando a WAN virtual do Azure

Este artigo mostra como usar a WAN virtual para estabelecer uma conexão VPN IPsec/IKE de sua rede local para o Azure por meio do emparelhamento privado de um circuito do ExpressRoute. Isso pode fornecer um trânsito criptografado entre as redes locais e as redes virtuais do Azure no ExpressRoute, sem passar pela Internet pública ou usar endereços IP públicos.

## <a name="topology-and-routing"></a>Topologia e roteamento

O diagrama a seguir mostra um exemplo de VPN sobre conectividade de emparelhamento privado do ExpressRoute:

![VPN no ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

O diagrama mostra uma rede na rede local conectada ao gateway de VPN do Hub do Azure sobre o emparelhamento privado do ExpressRoute. O estabelecimento da conectividade é simples:

1. Estabelecer conectividade de ExpressRoute com um circuito de ExpressRoute e emparelhamento privado
2. Estabeleça a conectividade VPN conforme descrito neste documento

Um aspecto importante dessa configuração é o roteamento entre as redes locais e o Azure por meio dos caminhos de ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego de redes locais para o Azure

Para o tráfego de redes locais para o Azure, os prefixos do Azure (incluindo o Hub virtual e todas as redes virtuais spoke conectadas ao Hub) serão anunciados por meio do BGP de emparelhamento privado do ExpressRoute e do BGP VPN. Isso resultará em duas rotas de rede (caminhos) para o Azure a partir das redes locais; um no caminho protegido por IPsec e um diretamente por meio do ExpressRoute **sem** proteção de IPSec. Para garantir que a criptografia seja aplicada à comunicação, você deve certificar-se de que, para a rede conectada VPN no diagrama, as rotas do Azure por meio do gateway de VPN local são preferenciais sobre o caminho direto do ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego do Azure para redes locais

O mesmo requisito se aplica ao tráfego do Azure para redes locais. Para garantir que o caminho IPsec seja preferencial em relação ao caminho direto do ExpressRoute (sem IPsec), você tem duas opções:

- Anuncie prefixos mais específicos na sessão BGP VPN para a rede conectada VPN. Você pode anunciar um intervalo maior que abrange a "rede conectada VPN" por meio do emparelhamento privado do ExpressRoute e intervalos mais específicos na sessão BGP VPN. Por exemplo, Anunci 10.0.0.0/16 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

- Anuncie prefixos não contíguos para VPN e ExpressRoute. Se os intervalos de rede conectados à VPN forem separados de outra rede conectada do ExpressRoute, você poderá anunciar os prefixos nas sessões VPN e BGP do ExpressRoute, respectivamente. Por exemplo, Anunci 10.0.0.0/24 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

Em ambos os exemplos, o Azure enviará tráfego para 10.0.1.0/24 pela conexão VPN em vez de diretamente pelo ExpressRoute sem proteção de VPN.

> [!WARNING]
> Se você anunciar os **mesmos** prefixos em conexões de EXPRESSROUTE e VPN, **o Azure usará o caminho do expressroute diretamente sem a proteção de VPN**.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. criar uma WAN virtual e um hub com gateways

Os seguintes recursos do Azure e as configurações locais correspondentes devem estar em vigor antes de continuar:

1. Uma WAN virtual do Azure
2. Um hub de WAN virtual com um [Gateway de ExpressRoute](virtual-wan-expressroute-portal.md) e um gateway de [VPN](virtual-wan-site-to-site-portal.md)

Consulte [criar uma associação de ExpressRoute usando a WAN virtual do Azure](virtual-wan-expressroute-portal.md) para obter as etapas para criar uma WAN virtual do Azure e um hub com uma associação de ExpressRoute e [criar uma conexão site a site usando a WAN virtual do Azure](virtual-wan-site-to-site-portal.md) para obter as etapas para criar uma VPN Gateway na WAN virtual.

## <a name="site"></a>2. criar um site para a rede local

O recurso do site é o mesmo que os sites VPN não ExpressRoute para a WAN virtual. O importante a observar é que o endereço IP do dispositivo VPN local agora pode ser um endereço IP privado ou um endereço IP público na rede local acessível por meio do emparelhamento privado do ExpressRoute criado na etapa 1.

> [!NOTE]
> O endereço IP do dispositivo VPN local deve fazer parte dos prefixos de endereço anunciados para o Hub WAN virtual por meio do emparelhamento privado do Azure ExpressRoute.
>

1. Navegue até o portal do Azure no navegador. Clique na WAN que você criou. Na página WAN, em **conectividade**, clique em **sites VPN** para abrir a página sites VPN.

2. Na página **Sites VPN**, clique em **+Criar site**.

3. Na página **Criar site**, preencha os seguintes campos:

   * **Assinatura**: verifique a assinatura.
   * **Grupo de recursos** – selecione ou crie o grupo de recursos que você deseja usar.
   * **Região** -a região do Azure para o recurso de site de VPN.
   * **Nome**: o nome que você deseja dar ao site local.
   * **Fornecedor do dispositivo** -o fornecedor do dispositivo VPN local.
   * **Border Gateway Protocol** -selecione "habilitar" se sua rede local usar o BGP.
   * **Espaço de endereço privado:** espaço de endereço localizado no local. O tráfego destinado a esse espaço de endereço é roteado para a rede local por meio do gateway de VPN.
   * **Hubs** -selecione um ou mais hubs para conectar este site de VPN. Os hubs selecionados devem ter gateways de VPN já criados.

4. Clique em **Avançar: Links >** para as configurações do link VPN:

   * **Nome do link** -o nome pelo qual você deseja se referir a essa conexão.
   * **Nome do provedor** -o nome do provedor de serviços de Internet para este site. No caso da rede local do ExpressRoute, o nome do provedor de serviços do ExpressRoute.
   * **Velocidade** -a velocidade do link do serviço de Internet ou do circuito do ExpressRoute.
   * **Endereço IP** -o endereço IP público do dispositivo VPN que reside em seu site local. Ou, no caso do ExpressRoute local, o endereço IP privado do dispositivo VPN por meio do ExpressRoute.

   Se o BGP estiver habilitado, ele será aplicado a todas as conexões criadas para este site no Azure. Configurar o BGP em uma WAN Virtual é equivalente a configurar o BGP em um gateway de VPN do Azure. Seu endereço de par de BGP local *não deve* ser o mesmo que o endereço IP de sua VPN para dispositivo ou o espaço de endereço de VNet do site de VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, ele *não pode* ser um endereço APIPA (169.254.*x*.*x*). Especifique esse endereço no Gateway de Rede Local correspondente que representa o local. Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Clique em **Avançar: examinar + criar >** para verificar os valores de configuração e criar o site VPN. Se você selecionou **hubs** para se conectar, a conexão será estabelecida entre a rede local e o gateway de VPN de Hub.

## <a name="hub"></a>3. atualize a configuração de conexão VPN para usar o ExpressRoute

Depois de criar o site VPN e conectar-se ao Hub, use as seguintes etapas para configurar a conexão para usar o emparelhamento privado do ExpressRoute:

1. Volte para a página de recursos de WAN virtual e clique no recurso de Hub. Ou navegue do site VPN para o Hub conectado.

2. Em **conectividade**, clique em **VPN (site a site)**

3. Clique em "..." no site VPN no ExpressRoute e selecione "**Editar conexão VPN para este Hub**"

4. Selecione "Sim" em "**usar endereço IP privado do Azure**". A configuração configura o gateway de VPN do hub para usar endereços IP privados dentro do intervalo de endereços do Hub no gateway para essa conexão, em vez dos endereços IP públicos. Isso garantirá que o tráfego da rede local percorra os caminhos de emparelhamento privado do ExpressRoute em vez de usar a Internet pública para essa conexão VPN. A captura de tela a seguir mostra a janela de configuração.

   ![Configuração de conexão VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Clique em **Salvar**.

Depois de salvo, o gateway de VPN de Hub usará os endereços IP privados no gateway de VPN para estabelecer as conexões IPsec/IKE com o dispositivo VPN local no ExpressRoute.

## <a name="associate"></a>4. obter os endereços IP privados do gateway de VPN do Hub

Baixe a configuração do dispositivo VPN para obter os endereços IP privados do gateway de VPN do Hub. Eles são necessários para configurar o dispositivo VPN local.

1. Na página do Hub, clique em **VPN (site a site)** em **conectividade**

2. Na parte superior da página Visão geral, clique em **baixar configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos ' Microsoft-Network-[Location] ', em que Location é o local da WAN. Depois de aplicar a configuração a seus dispositivos VPN, você poderá excluir essa conta de armazenamento.

3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.

4. Aplique a configuração ao dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Entender o arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections**: essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** dos hubs virtuais da VNet<br/>Exemplo:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Espaço de endereço** das VNets conectadas ao hub<br>Exemplo:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **Endereços IP** do vpngateway do hub virtual. Como cada conexão do vpngateway é composta por dois túneis na configuração ativa-ativa, você vê os dois endereços IP listados neste arquivo. Neste exemplo, você vê "Instance0" e "instance1" para cada site e são endereços IP privados em vez de endereços IP públicos.<br>Exemplo:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Detalhes de configuração de conexão Vpngateway** , como BGP, chave pré-compartilhada, etc. A PSK é a chave pré-compartilhada que é gerada automaticamente para você. Você sempre pode editar a conexão na página Visão Geral de um PSK personalizado.
  
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

* As instruções na página de dispositivos VPN não foram criadas para WAN Virtual, mas você pode usar os valores de WAN Virtual do arquivo de configuração para configurar seu dispositivo VPN manualmente. 
* Os scripts de configuração de dispositivo que podem ser baixados para o Gateway de VPN não funcionam para WAN Virtual, já que a configuração é diferente.
* Uma nova WAN Virtual pode dar suporte a IKEv1 e IKEv2.
* A WAN Virtual só pode usar dispositivos VPN baseados em rota e instruções do dispositivo.

## <a name="viewwan"></a>5. exibir sua WAN virtual

1. Navegue até a WAN virtual.

2. Na página de visão geral, cada ponto do mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub.

3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="viewhealth"></a>6. exibir o Resource Health

1. Navegue até a WAN.

2. Na página de sua WAN, na seção **SUPORTE + Solução de problemas**, clique em **Integridade** e exiba seu recurso.

## <a name="connectmon"></a>7. monitorar uma conexão

Crie uma conexão para monitorar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>8. limpar recursos

Quando esses recursos não forem mais necessários, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Este artigo ajuda você a criar uma conexão VPN no emparelhamento privado do ExpressRoute usando a WAN virtual. Para saber mais sobre WAN virtual e outros recursos relacionados, consulte a página [visão geral da WAN virtual](virtual-wan-about.md) .
