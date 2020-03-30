---
title: Solução Azure VMware by CloudSimple - Conecte nuvem privada à rede Azure usando expressRoute
description: Descreve como conectar seu ambiente CloudSimple Private Cloud à rede virtual do Azure usando o ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015228"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Conecte seu ambiente CloudSimple Private Cloud à rede virtual Do Azure usando o ExpressRoute

Sua CloudSimple Private Cloud pode ser conectada à sua rede virtual Azure usando o Azure ExpressRoute.  Essa conexão de baixa latência de alta largura de banda permite que você acesse serviços em execução em sua assinatura do Azure a partir do seu ambiente Private Cloud.

A conexão de rede virtual permite:

* Use o Azure como um alvo de backup para máquinas virtuais em sua Nuvem Privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu armazenamento de dados private cloud vSAN.
* Use aplicativos híbridos onde o nível web do aplicativo é executado na nuvem pública enquanto os níveis de aplicativo e banco de dados são executados em sua Nuvem Privada.

![Conexão Azure ExpressRoute para rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configure uma conexão de rede virtual

Para configurar a conexão de rede virtual com sua Nuvem Privada, você precisa de sua chave de autorização, uri do circuito de pares e acesso à sua assinatura do Azure. Essas informações estão disponíveis na página Conexão de Rede Virtual no portal CloudSimple. Para obter instruções, [consulte Obter informações de peering para a rede virtual Do Zure para a conexão CloudSimple](virtual-network-connection.md). Se você tiver algum problema para obter as informações, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.

> [!TIP]
> Se você já tem uma rede virtual Azure, uma sub-rede de gateway e um gateway de rede virtual, você pode pular para o passo 4.

1. Crie uma rede virtual em sua assinatura do Azure e verifique se o espaço de endereço selecionado é diferente do espaço de endereço da sua Nuvem Privada.  Se você já tem uma rede virtual Azure, você pode usar a já existente.  Para obter detalhes, consulte [Criar uma rede virtual usando o portal Azure](../virtual-network/quick-create-portal.md).
2. Crie a sub-rede gateway em sua rede virtual Azure.  Se você já tem uma sub-rede gateway em sua rede virtual Azure, você pode usar a já existente. Para obter detalhes, consulte [Criar a sub-rede gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Crie o gateway de rede virtual em sua rede virtual.  Se você tiver um gateway de rede virtual existente, você pode usar o existente. Para obter detalhes, consulte [Criar o gateway de rede virtual](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Crie a conexão entre sua rede virtual e sua Nuvem Privada resgatando a chave de autorização conforme descrito em [Conectar uma rede virtual a um circuito - assinatura diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se você estiver usando um gateway de rede virtual existente e ele tiver uma conexão ExpressRoute com o mesmo local do circuito CloudSimple ExpressRoute, a conexão não será estabelecida.  Crie uma nova rede virtual e siga os passos anteriores.

## <a name="test-the-virtual-network-connection"></a>Teste a conexão de rede virtual

Depois que a conexão for criada, você pode verificar o status da conexão selecionando **Propriedades** em **Configurações**.  Status e Estado de Provisionamento devem mostrar **Sucesso**.

![Status da conexão](media/azure-expressroute-connection.png)

Para testar a conexão de rede virtual:

1. Crie uma máquina virtual em sua assinatura do Azure.
2. Encontre o endereço IP do seu vCenter privado da Nuvem (consulte seu e-mail de boas-vindas).
3. Ping seu Cloud vCenter a partir da máquina virtual criada em sua rede virtual Azure.
4. Ping sua máquina virtual Azure a partir de uma máquina virtual em execução em seu vCenter Private Cloud.

Se você tiver algum problema para estabelecer a conexão, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.
