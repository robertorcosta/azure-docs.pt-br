---
title: Solução VMware por CloudSimple - Conexões de rede Azure
description: Saiba como conectar sua rede virtual Do Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025071"
---
# <a name="azure-network-connections-overview"></a>Visão geral das conexões de rede do Azure

Quando você cria um serviço CloudSimple em uma região e cria nós, você pode:

* Solicite um circuito Azure ExpressRoute e conecte-o à rede CloudSimple nessa região.
* Conecte sua rede de região CloudSimple à sua rede virtual Azure ou à sua rede local usando o Azure ExpressRoute.
* Forneça acesso aos serviços em execução em sua assinatura do Azure ou em sua rede local a partir do seu ambiente Private Cloud.

A conexão ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Benefícios

A conexão de rede Azure permite:

* Use o Azure como um alvo de backup para máquinas virtuais em sua Nuvem Privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu armazenamento de dados private cloud vSAN.
* Use aplicativos híbridos onde o nível web do aplicativo é executado na nuvem pública enquanto os níveis de aplicativo e banco de dados são executados em sua Nuvem Privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual do Azure

As nuvens privadas podem ser conectadas aos recursos do Azure usando o ExpressRoute.  A conexão ExpressRoute permite que você acesse recursos em execução em sua assinatura do Azure a partir de sua Nuvem Privada.  Essa conexão permite que você amplie sua rede Private Cloud para sua rede virtual Azure.  As rotas da rede CloudSimple serão trocadas com sua rede virtual Azure via BGP.  Se você tiver a rede virtual configurada, todas as redes virtuais espiadas estarão acessíveis a partir de sua rede CloudSimple.

![Conexão Azure ExpressRoute para rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão ExpressRoute com a rede local

Você pode conectar o circuito Azure ExpressRoute existente à sua região CloudSimple. O recurso ExpressRoute Global Reach é usado para conectar os dois circuitos entre si.  Uma conexão é estabelecida entre os circuitos on-premises e CloudSimple ExpressRoute.  Essa conexão permite que você amplie suas redes locais para a rede Private Cloud. As rotas da sua rede CloudSimple serão trocadas via BGP com sua rede local.

![Conexão ExpressRoute no local - Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Conexão com a rede local e a rede virtual Do Zure

Conexões com a rede local e a rede virtual Azure podem coexistir a partir de sua rede CloudSimple.  A conexão usa BGP para trocar rotas entre rede local, rede virtual Azure e rede CloudSimple.  Quando você conectar sua rede CloudSimple à sua rede virtual Azure na presença da conexão Global Reach, as rotas de rede virtuais do Azure serão visíveis em sua rede local.  A troca de rotas acontece no Azure entre os roteadores de borda.

![Conexão expressroute no local com conexão de rede virtual do Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerações importantes

Conectar-se à rede CloudSimple a partir da rede local e da rede virtual Do Azure permite a troca de rotas entre todas as redes.

* A rede virtual Azure será visível tanto da rede local quanto da rede CloudSimple.
* Se você se conectou à sua rede virtual Azure a partir da rede local, a conexão com a rede CloudSimple usando o Global Reach permitirá o acesso a redes virtuais da rede CloudSimple.
* Os endereços de **sub-rede não devem** se sobrepor entre nenhuma das redes conectadas.
* O CloudSimple **não** anunciará a rota padrão para as conexões ExpressRoute
* Se o roteador no local anunciar a rota padrão, o tráfego da rede CloudSimple e da rede virtual Azure usará a rota padrão anunciada.  Como resultado, as máquinas virtuais no Azure não podem ser acessadas usando endereços IP públicos.

## <a name="next-steps"></a>Próximas etapas

* [Conecte a rede virtual do Azure ao CloudSimple usando o ExpressRoute](virtual-network-connection.md)
* [Conecte-se de on-premises ao CloudSimple usando expressroute](on-premises-connection.md)
