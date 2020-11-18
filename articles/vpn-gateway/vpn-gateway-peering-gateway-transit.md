---
title: Configurar o tráfego do gateway de VPN para o emparelhamento de rede virtual
description: Configure o tráfego de gateway para o emparelhamento de rede virtual, para conectar diretamente duas redes virtuais do Azure em uma para fins de conectividade.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 3f90ccbc98dac957fcb9fb81cd24c7ae6c9ac664
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657543"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurar o tráfego do gateway de VPN para o emparelhamento de rede virtual

Este artigo ajuda você a configurar o tráfego de gateway para o emparelhamento de rede virtual. O [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) conecta diretamente duas redes virtuais do Azure, mesclando as duas redes virtuais em uma para fins de conectividade. [Tráfego de gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) é uma propriedade de emparelhamento que permite que uma rede virtual para utilizar o gateway de VPN na rede virtual emparelhada para conectividade entre locais e VNET a VNET. O diagrama a seguir mostra como o tráfego de gateway funciona com o emparelhamento de rede virtual.

![trânsito de gateway](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

No diagrama, o trânsito de gateway permite que as redes virtuais emparelhadas usem o gateway de VPN do Azure no Hub-RM. A conectividade disponível no gateway de VPN, incluindo as conexões S2S, P2S e VNET a VNET, aplica-se a todas as três redes virtuais. A opção de trânsito está disponível para o emparelhamento entre modelos de implantação iguais ou diferentes. A restrição é que o gateway de VPN pode estar apenas na rede virtual que usa o modelo de implantação do Resource Manager, conforme mostrado no diagrama.

Na arquitetura de rede de hub e spoke, um trânsito de gateway permite que redes virtuais de spoke compartilhem o gateway de VPN no hub, em vez de implantar gateways de VPN em cada rede virtual de spoke. As rotas para as redes virtuais conectadas pelo gateway ou das redes locais serão propagadas às tabelas de roteamento para as redes virtuais emparelhadas usando o trânsito de gateway. Você pode desabilitar a propagação automática de rota no gateway de VPN. Crie uma tabela de roteamento com a opção "**Desabilitar propagação de rotas BGP**" e associe a tabela de roteamento às sub-redes para evitar a distribuição de rota para essas sub-redes. Para obter mais informações, consulte [Tabela de roteamento de rede virtual](../virtual-network/manage-route-table.md).

Há dois cenários descritos neste documento:

1. Ambas as redes virtuais estão usando o modelo de implantação do Resource Manager
2. A rede virtual de spoke é clássica e a rede virtual de hub com o gateway está no Resource Manager


>[!NOTE]
> Se você fizer uma alteração na topologia de rede e tiver clientes VPN do Windows, o pacote de cliente VPN para clientes do Windows deve ser baixado e instalado novamente para que as alterações sejam aplicadas ao cliente.
>

## <a name="requirements"></a>Requisitos



O exemplo deste documento exige a criação dos seguintes recursos:

1. Rede virtual Hub-RM com um gateway de VPN
2. Rede virtual Spoke-RM
3. Rede virtual Spoke-Classic com o modelo de implantação clássico
4. A conta usada exige as funções e as permissões necessárias. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.

Veja os seguintes documentos para obter instruções:

1. [Criar um gateway de VPN e uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Criar um emparelhamento de rede virtual com o mesmo modelo de implantação](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Criar um emparelhamento de rede virtual com diferentes modelos de implantação](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a><a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual precisam ter as funções ou as permissões necessárias. No exemplo abaixo, se você emparelhar duas redes virtuais chamadas Hub-RM e Spoke-Classic, sua conta deverá ter as seguintes funções ou permissões para cada rede virtual:
    
|Rede virtual|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|Hub-RM|Gerenciador de Recursos|[Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|Spoke-Classic|Gerenciador de Recursos|[Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%252fazure%252fvirtual-network%252ftoc.json) (somente para o Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Emparelhamento do Resource Manager para o Resource Manager com o tráfego de gateway

Siga as instruções para criar ou atualizar os emparelhamentos de rede virtual para permitir o tráfego de gateway.

1. Crie ou atualize o emparelhamento de rede virtual de Spoke-RM para Hub-RM no portal do Azure. Navegue para o recurso de rede virtual Spoke-RM, clique em "Emparelhamentos" e, em seguida, em "Adicionar":
    - Definir a opção "Resource Manager"
    - Selecione a rede virtual Hub-RM na assinatura correspondente
    - Verifique se a opção "Permitir acesso à rede virtual" está "Habilitada"
    - Definir a opção "**Usar gateways remotos**"
    - Clique em "OK"

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Se o emparelhamento já foi criado, navegue para o recurso de emparelhamento e, em seguida, habilite a opção "**Usar gateways remotos**" semelhante à captura de tela mostrada na etapa (1)

3. Crie ou atualize o emparelhamento de rede virtual de Hub-RM para Spoke-RM no portal do Azure. Navegue para o recurso de rede virtual do Hub-RM, clique em "Emparelhamentos" e, em seguida, em "Adicionar":
    - Definir a opção "Resource Manager"
    - Verifique se a opção "Permitir acesso à rede virtual" está "Habilitada"
    - Selecione a rede virtual "Spoke-RM" na assinatura correspondente
    - Definir a opção "**Permitir tráfego de gateway**"
    - Clique em "OK"

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Se o emparelhamento já foi criado, navegue para o recurso de emparelhamento e, em seguida, habilite a opção "**Permitir tráfego de gateway**" semelhante à captura de tela mostrada na etapa (3)

5. Verifique o status de emparelhamento como "**Conectado**" em ambas as redes virtuais

### <a name="powershell-sample"></a>Exemplo do PowerShell

Use também o PowerShell para criar ou atualizar o emparelhamento com o exemplo acima. Substitua as variáveis pelos nomes das redes virtuais e dos grupos de recursos.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Emparelhamento do Clássico para o Resource Manager com o tráfego de gateway

As etapas são semelhantes ao exemplo do Resource Manager, exceto que as operações são aplicadas somente na rede virtual Hub-RM.

1. Crie ou atualize o emparelhamento de rede virtual de Hub-RM para Spoke-RM no portal do Azure. Navegue para o recurso de rede virtual do Hub-RM, clique em "Emparelhamentos" e, em seguida, em "Adicionar":
   - Definir a opção "Clássico" para o modelo de implantação de Rede virtual
   - Selecione a rede virtual "Spoke-Classic" na assinatura correspondente
   - Verifique se a opção "Permitir acesso à rede virtual" está "Habilitada"
   - Definir a opção "**Permitir tráfego de gateway**"
   - Clique em "OK"

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Se o emparelhamento já foi criado, navegue para o recurso de emparelhamento e, em seguida, habilite a opção "**Permitir tráfego de gateway**" semelhante à captura de tela mostrada na etapa (1)

3. Não há nenhuma operação na rede virtual Spoke-Classic

4. Verifique o status de emparelhamento como "**Conectado**" em uma rede virtual Hub-RM

Depois que o status mostrar "Conectado", as redes virtuais de spoke poderão começar a usar a conectividade entre locais ou VNET a VNET por meio do gateway de VPN na rede virtual de hub.

### <a name="powershell-sample"></a>Exemplo do PowerShell

Use também o PowerShell para criar ou atualizar o emparelhamento com o exemplo acima. Substitua a variáveis e a ID da assinatura pelos valores de rede virtual, dos grupos de recursos e da assinatura. Você precisa apenas criar o emparelhamento de rede virtual em uma rede virtual de hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as restrições e os comportamentos do emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e [as configurações de emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#create-a-peering) antes de criar um emparelhamento de rede virtual para uso em produção.
* Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com o emparelhamento de rede virtual e o trânsito de gateway.