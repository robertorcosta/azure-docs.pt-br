---
title: 'Azure ExpressRoute: Adicione um gateway a um VNet: PowerShell'
description: Este artigo ensina como adicionar gateway de VNet a uma VNet do Resource Manager já existente do ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037422"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurar um gateway de rede virtual para ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo ensina como adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma rede virtual já existente. As etapas para esta configuração se aplicam aos VNets que foram criados usando o modelo de implantação do Resource Manager para uma configuração ExpressRoute. Para obter mais informações, confira [Sobre os gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Antes de começar

### <a name="working-with-powershell"></a>Trabalhando com powershell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista de referência de configuração

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito do ExpressRoute. Consulte [Vincular uma Rede Virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).
