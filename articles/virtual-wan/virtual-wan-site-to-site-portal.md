---
title: 'WAN Virtual do Azure: Criar conexões site a site'
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN site a site para o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e17205af1ede845ea77b04f6f2b4c6babf3bc450
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482137"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar aos recursos do Azure em uma conexão de VPN IPsec/IKE (IKEv1 e IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma WAN virtual
> * Criar um hub
> * Criar um site
> * Conectar um site a um hub
> * Conectar um site VPN a um hub
> * Conectar uma VNET a um hub
> * Baixar um arquivo de configuração
> * Exibir a WAN virtual

> [!NOTE]
> Se você tem vários sites, normalmente usaria um [parceiro de WAN Virtual](https://aka.ms/virtualwan) para criar esta configuração. No entanto, você poderá criar essa configuração por conta própria se estiver familiarizado com a rede e tiver domínio sobre a configuração de seu próprio dispositivo VPN.
>

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Criar uma WAN virtual

Em um navegador, acesse o portal do Azure e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, clique em **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e clique em Enter.
2. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Criar WAN.
3. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Local do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo:** Básico ou Padrão. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
4. Quando terminar de preencher os campos, selecione **Revisar + Criar**.
5. Depois que a validação for aprovada, selecione **Criar** para criar a WAN virtual.

## <a name="hub"></a>Criar um hub

Um hub é uma rede virtual que pode conter gateways para funcionalidade site a site, ExpressRoute ou ponto a site. Depois que o hub é criado, você será cobrado por ele, mesmo se não anexar sites. Leva 30 minutos para criar o gateway de VPN site a site no hub virtual.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>Criar um site

Agora você está pronto para criar os sites correspondentes aos seus locais físicos. Crie quantos sites forem necessários para corresponder a seus locais físicos. Por exemplo, se você tiver uma filial em Nova York, uma filial em Londres e uma filial em Los Angeles, crie três sites separados. Esses sites contêm seus pontos de extremidade do dispositivo VPN local. Você pode criar até 1000 sites por hub virtual em uma WAN virtual. No caso de vários hubs, você pode criar 1000 para cada um desses hubs. Se tiver um dispositivo CPE de parceiro de WAN Virtual (inserção de link), verifique com ele para saber mais sobre a automação no Azure. Normalmente, a automação implica em uma experiência simples de clique para exportar informações de branch em grande escala para o Azure e configurar a conectividade do CPE para o gateway de VPN da WAN Virtual do Azure (aqui está um link para as orientações sobre automação do Azure para os parceiros CPE).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>Conectar o site VPN ao hub

Nesta etapa, você conecta o site VPN ao hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>Conectar a VNet ao hub

Nesta etapa, você pode criar a conexão entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de rede virtual.

## <a name="device"></a>Baixar configuração de VPN

Use a configuração do dispositivo VPN para configurar seu dispositivo VPN local.

1. Na página de sua WAN virtual, clique em **Visão Geral**.
2. Na parte superior da página **Hub ->VPNSite**, clique em **Baixar a Configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos 'microsoft-network-[local]', em que local é o local da rede remota. Depois de aplicar a configuração a seus dispositivos VPN, você poderá excluir essa conta de armazenamento.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Aplique a configuração ao dispositivo de VPN local.

### <a name="understanding-the-vpn-device-configuration-file"></a>Entender o arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections**: essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** dos hubs virtuais da VNet<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Endereços IP** do hub virtual vpngateway. Como cada conexão do vpngateway é composta por dois túneis na configuração ativa-ativa, você vê os dois endereços IP listados neste arquivo. Neste exemplo, você vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes da configuração da conexão do Vpngateway** como BGP, chave pré-compartilhada, etc. O PSK é a chave pré-compartilhada gerada automaticamente para você. Você sempre pode editar a conexão na página Visão Geral de um PSK personalizado.
  
### <a name="example-device-configuration-file"></a>Exemplo de arquivo de configuração de dispositivo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

>[!NOTE]
> Se você estiver trabalhando com uma solução de parceiro de WAN Virtual, a configuração do dispositivo VPN ocorrerá automaticamente. O controlador do dispositivo obtém o arquivo de configuração do Azure e o aplica ao dispositivo para configurar a conexão com o Azure. Isso significa que você não precisa saber como configurar seu dispositivo VPN manualmente.
>

Se você precisar de instruções para configurar o dispositivo, poderá usar as instruções sobre a [página de scripts de configuração de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) com as seguintes condições:

* As instruções na página de dispositivos VPN não foram criadas para WAN Virtual, mas você pode usar os valores de WAN Virtual do arquivo de configuração para configurar seu dispositivo VPN manualmente. 
* Os scripts de configuração de dispositivo que podem ser baixados para o Gateway de VPN não funcionam para WAN Virtual, já que a configuração é diferente.
* Uma nova WAN Virtual pode oferecer suporte a IKEv1 e IKEv2.
* A WAN Virtual pode usar dispositivos VPN baseados em rota e baseados em política e instruções do dispositivo.

## <a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página **Visão Geral**, cada ponto no mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub, o status da conexão e a entrada e saída de bytes.
3. Na seção Hubs e conexões, você pode exibir o status do hub, sites VPN, etc. Clique em um nome de hub específico e navegue até o site VPN para saber mais.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
