---
title: 'Gateway de VPN do Azure: Criar e gerenciar conexões VPN S2S: Tutorial'
description: Tutorial - criar e gerenciar VMs Windows com o módulo do Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: c42d164647d608fc45e1135cd4111ff3900a733c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137153"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Tutorial: Criar e gerenciar conexões VPN S2S usando o PowerShell

As conexões VPN S2S do Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial orienta na conexão dos ciclos de vida VPN S2S de IPsec, como criar e gerenciar uma conexão de VPN S2S. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma conexão VPN S2S
> * Atualizar a propriedade de conexão: chave pré-compartilhada, BGP, política IPsec/IKE
> * Adicionar mais conexões VPN
> * Excluir uma conexão VPN

O diagrama a seguir mostra a topologia para este tutorial:

![Diagrama de conexão VPN site a site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Trabalhar com o Azure Cloud Shell e Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>Requisitos

Conclua o tutorial primeiro: [Criar um gateway de VPN com o Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md) para criar os seguintes recursos:

1. Grupo de recursos (TestRG1), rede virtual (VNet1) e a GatewaySubnet
2. Gateway VPN (VNet1GW)

Os valores de parâmetro de rede virtual estão listados abaixo. Observe os valores adicionais para o gateway de rede local que representam a rede local. Altere os valores abaixo com base na configuração da rede e do ambiente e, em seguida, copie-os e cole-os para definir as variáveis para este tutorial. Se a sessão do Cloud Shell atingir o tempo limite ou você precisar usar outra janela do PowerShell, copie e cole as variáveis na nova sessão e continue o tutorial.

>[!NOTE]
> Se estiver usando isso para estabelecer uma conexão, altere os valores para que correspondam à rede local. Se você estiver apenas executando essas etapas como um tutorial, não precisará fazer alterações, mas a conexão não funcionará.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

O fluxo de trabalho para criar uma conexão VPN S2S é simples:

1. Criar um gateway de rede local para representar rede local
2. Criar uma conexão VPN do Microsoft Azure entre o gateway de VPN do Azure e o gateway de rede local

## <a name="create-a-local-network-gateway"></a>Criar um gateway de rede local

Criar um gateway de rede local para representar a sua rede local. Você pode especificar as propriedades de sua rede local no gateway de rede local, incluindo:

* Endereço IP público de seu gateway de VPN.
* Espaço de endereço local
* (Opcional) Atributos BGP (endereço IP do par BGP e número AS)

Crie um gateway de rede local com o comando [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway).

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Criar uma conexão VPN S2S

Em seguida, crie uma conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN com [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Observe que o '-ConnectionType' para VPN Site a Site é *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

Adicionar a propriedade opcional " **- EnableBGP $True**" para habilitar BGP para a conexão, se você estiver usando o BGP. Isso está desabilitado por padrão. O parâmetro '-ConnectionProtocol ' é opcional com o IKEv2 por padrão. Você pode criar a conexão com protocolos IKEv1 especificando **-ConnectionProtocol IKEv1**.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Atualizar a chave pré-compartilhada de conexão VPN, BGP, política IPsec/IKE

### <a name="view-and-update-your-pre-shared-key"></a>Exibir e atualizar a chave pré-compartilhada

A conexão de VPN S2S do Azure usa uma chave pré-compartilhada (secreta) para autenticar entre o dispositivo VPN no local e o gateway de VPN do Microsoft Azure. Exiba e atualize a chave pré-compartilhada para uma conexão com [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) e [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> A chave pré-compartilhada é uma cadeia de caracteres de **caracteres ASCII imprimíveis** não mais de 128 de comprimento.

Este comando mostra a chave pré-compartilhada para a conexão:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

A saída será "**Azure\@!b2C3**" seguindo o exemplo acima. Use o comando abaixo para alterar o valor da chave pré-compartilhada para "**Azure\@!_b2=C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Habilitar o BGP em conexão de VPN

O Gateway de VPN do Microsoft Azure oferece suporte ao protocolo de roteamento BGP. Você pode habilitar o BGP em cada conexão individual, dependendo se você estiver usando o BGP em seus dispositivos e redes locais. Especifique as seguintes propriedades BGP antes de habilitar o BGP na conexão:

* Azure VPN ASN (Número de Sistema Autônomo)
* Gateway de rede loca ASN
* Gateway de rede local BGP par de endereço IP

Se você não tiver configurado as propriedades do BGP, os seguintes comandos adicionarão essas propriedades ao gateway de VPN e ao gateway de rede local: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) e [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

Use o seguinte exemplo para configurar as propriedades do BGP:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Habilite o BGP com [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Você pode desabilitar BGP pela alteração do valor de propriedade "-EnableBGP" para **$False** Consulte em [de BGP em gateways de VPN do Azure](vpn-gateway-bgp-resource-manager-ps.md) para obter mais explicações sobre BGP em gateways de VPN do Azure.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Aplicar uma política personalizada da política IPsec/IKE na conexão

Você pode aplicar uma política de IPsec/IKE opcional para especificar a combinação exata de algoritmos de criptografia IPsec/IKE e restrições de chave de conexão, em vez de usar as [propostas padrão](vpn-gateway-about-vpn-devices.md#ipsec). O script de exemplo a seguir cria uma política de IPsec/IKE diferente com os parâmetros e os algoritmos seguintes:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, SA Tempo de vida 14.400 segundos e 102.400.000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Consulte [política IPsec/IKE para conexões S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter uma lista de algoritmos e instruções.

## <a name="add-another-s2s-vpn-connection"></a>Adicionar outra conexão VPN S2S

Adicione outra conexão VPN S2S ao mesmo gateway de VPN, crie outro gateway de rede local e crie uma conexão entre o novo gateway de rede local e o gateway de VPN. Use os exemplos a seguir, lembrando-se de modificar as variáveis para que reflitam sua própria configuração da rede.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Agora há duas conexões VPN S2S para o gateway de VPN do Azure.

![Conexões VPN de multissite](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Excluir uma conexão VPN S2S

Exclua uma conexão de VPN S2S com [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Excluir o gateway de rede local se você não precisar mais. Você não pode excluir um gateway de rede local se houver outras conexões associadas a ele.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se essa configuração fizer parte de uma implantação de protótipo, teste ou prova de conceito, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o gateway de VPN e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar e gerenciar conexões VPN S2S por exemplo, como:

> [!div class="checklist"]
> * Criar uma conexão VPN S2S
> * Atualizar a propriedade de conexão: chave pré-compartilhada, BGP, política IPsec/IKE
> * Adicionar mais conexões VPN
> * Excluir uma conexão VPN

Ir para os tutoriais a seguir para saber mais sobre conexões de P2S, S2S e VNet para VNet.

> [!div class="nextstepaction"]
> * [Criar conexões VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar conexões P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
