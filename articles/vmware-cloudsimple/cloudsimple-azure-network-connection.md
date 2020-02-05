---
title: Soluções VMware (AVS)-conexões de rede do Azure
description: Saiba como conectar sua rede virtual do Azure à sua rede de região da AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025071"
---
# <a name="azure-network-connections-overview"></a>Visão geral das conexões de rede do Azure

Ao criar um serviço AVS em uma região e criar nós, você pode:

* Solicite um circuito do Azure ExpressRoute e anexe-o à rede AVS nessa região.
* Conecte sua rede de região AVS à sua rede virtual do Azure ou à sua rede local usando o Azure ExpressRoute.
* Forneça acesso aos serviços em execução na sua assinatura do Azure ou na rede local de seu ambiente de nuvem privada.

A conexão do ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Benefícios

A conexão de rede do Azure permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública da AVS, enquanto as camadas de aplicativo e banco de dados são executadas em sua nuvem privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual do Azure

As nuvens privadas da AVS podem ser conectadas aos recursos do Azure usando o ExpressRoute. A conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure da sua nuvem privada de AVS. Essa conexão permite que você estenda sua rede de nuvem privada de AVS para sua rede virtual do Azure. As rotas de uma rede AVS serão trocadas com sua rede virtual do Azure via BGP. Se você tiver configurado o emparelhamento de rede virtual, todas as redes virtuais emparelhadas poderão ser acessadas da rede AVS.

![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão do ExpressRoute à rede local

Você pode conectar seu circuito do Azure ExpressRoute existente à sua região da AVS. O recurso de Alcance Global ExpressRoute é usado para conectar os dois circuitos entre si. Uma conexão é estabelecida entre os circuitos do ExpressRoute no local e na AVS. Essa conexão permite que você estenda suas redes locais para uma rede de nuvem privada de AVS. As rotas de sua rede AVS serão trocadas por meio de BGP com sua rede local.

![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Conexão à rede local e à rede virtual do Azure

As conexões com a rede local e a rede virtual do Azure podem coexistir da rede AVS. A conexão usa BGP para trocar rotas entre uma rede local, a rede virtual do Azure e uma rede de AVS. Quando você conecta sua rede AVS à rede virtual do Azure na presença de uma conexão Alcance Global, as rotas de rede virtual do Azure ficarão visíveis em sua rede local. A troca de rota ocorre no Azure entre os roteadores de borda.

![Conexão do ExpressRoute local com a conexão de rede virtual do Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerações importantes

Conectar-se a uma rede AVS de uma rede local e/ou de uma rede virtual do Azure permite a troca de rotas entre todas as redes.

* Uma rede virtual do Azure será visível de uma rede local e de uma rede de sincronização automática.
* Se você se conectou à rede virtual do Azure de uma rede local, a conexão a uma rede AVS usando Alcance Global permitirá o acesso a redes virtuais da rede AVS.
* Os endereços de sub-rede **não devem** se sobrepor entre as redes conectadas.
* A AVS **não** anunciará uma rota padrão para as conexões do ExpressRoute
* Se o roteador local anunciar a rota padrão, o tráfego da rede AVS e da rede virtual do Azure usará a rota padrão anunciada. Como resultado, as máquinas virtuais no Azure não podem ser acessadas usando endereços IP públicos.

## <a name="next-steps"></a>Próximos passos

* [Conectar a rede virtual do Azure à AVS usando o ExpressRoute](virtual-network-connection.md)
* [Conectar-se do local para a AVS usando o ExpressRoute](on-premises-connection.md)
