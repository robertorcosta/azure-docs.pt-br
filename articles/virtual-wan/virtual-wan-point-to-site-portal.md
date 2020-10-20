---
title: 'Tutorial: Usar a WAN Virtual do Azure para criar uma conexão Ponto a Site com o Azure'
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN ponto a site com o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 3d03d0267ff4fb16042d5cc2016e87139b88281a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056575"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão de VPN do Usuário usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para obter mais informações sobre a WAN Virtual, consulte a [Visão Geral de WAN Virtual](virtual-wan-about.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Especificar servidores de DNS
> * Baixar um perfil de cliente VPN
> * Exibir a WAN virtual

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração P2S (ponto a site) define os parâmetros para conexão de clientes remotos.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Criar um hub com o gateway ponto a site

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar servidor DNS

Os gateways de VPN de Usuário da WAN Virtual permitem especificar até cinco servidores DNS. Você pode configurá-lo durante o processo de criação do hub ou modificá-lo posteriormente. Para fazer isso, localize o hub virtual. Em **VPN de Usuário (ponto a site)** , selecione **configurar** e insira os endereços IP do servidor DNS nas caixas de texto **Servidores DNS Personalizados**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Baixar o perfil de VPN

Use o perfil VPN para configurar seus clientes.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Configurar clientes VPN do usuário

Use o perfil baixado para configurar os clientes de acesso remoto. O procedimento para cada sistema operacional é diferente, siga as instruções que se aplicam ao seu sistema.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
1. Na página **Visão Geral**, cada ponto no mapa representa um hub.
1. Na seção **Hubs e conexões**, você pode ver o status do hub, o site, a região, o status de conexão de VPN e os bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, para saber mais sobre a WAN Virtual, confira:

> [!div class="nextstepaction"]
> * [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md)
