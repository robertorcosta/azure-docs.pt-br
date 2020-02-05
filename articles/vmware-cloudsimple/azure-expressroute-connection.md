---
title: Soluções VMware do Azure (AVS)-conectar a nuvem privada da AVS à rede do Azure usando o ExpressRoute
description: Descreve como conectar seu ambiente de nuvem privada da AVS à rede virtual do Azure usando o ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015228"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Conectar seu ambiente de nuvem privada de AVS à rede virtual do Azure usando o ExpressRoute

Sua nuvem privada de AVS pode ser conectada à sua rede virtual do Azure usando o Azure ExpressRoute. Essa alta largura de banda, conexão de baixa latência, permite que você acesse os serviços em execução na sua assinatura do Azure de seu ambiente de nuvem privada de AVS.

A conexão de rede virtual permite que você:

* Use o Azure como um destino de backup para máquinas virtuais na sua nuvem privada de AVS.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento de vSAN de nuvem privada da AVS.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública enquanto as camadas do aplicativo e do banco de dados são executadas em sua nuvem privada de AVS.

![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configurar uma conexão de rede virtual

Para configurar a conexão de rede virtual à sua nuvem privada da AVS, você precisa da sua chave de autorização, do URI do circuito par e do acesso à sua assinatura do Azure. Essas informações estão disponíveis na página conexão de rede virtual no portal da AVS. Para obter instruções, consulte [obter informações de emparelhamento para a rede virtual do Azure para conexão AVS](virtual-network-connection.md). Se você tiver problemas para obter as informações, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.

> [!TIP]
> Se você já tiver uma rede virtual do Azure, uma sub-rede de gateway e um gateway de rede virtual, poderá pular para a etapa 4.

1. Crie uma rede virtual em sua assinatura do Azure e verifique se o espaço de endereço selecionado é diferente do espaço de endereço da sua nuvem privada de AVS. Se você já tiver uma rede virtual do Azure, poderá usar a existente. Para obter detalhes, consulte [criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).
2. Crie a sub-rede de gateway em sua rede virtual do Azure. Se você já tiver uma sub-rede de gateway em sua rede virtual do Azure, poderá usar a existente. Para obter detalhes, consulte [criar a sub-rede de gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Crie o gateway de rede virtual em sua rede virtual. Se você tiver um gateway de rede virtual existente, poderá usar o existente. Para obter detalhes, consulte [criar o gateway de rede virtual](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Crie a conexão entre sua rede virtual e sua nuvem privada de AVS, resgatando a chave de autorização, conforme descrito em [conectar uma rede virtual a uma assinatura de circuito diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se você estiver usando um gateway de rede virtual existente e ele tiver uma conexão de ExpressRoute para o mesmo local que o circuito de ExpressRoute do AVS, a conexão não será estabelecida. Crie uma nova rede virtual e siga as etapas anteriores.

## <a name="test-the-virtual-network-connection"></a>Testar a conexão de rede virtual

Depois que a conexão é criada, você pode verificar o status da conexão selecionando **Propriedades** em **configurações**. O estado de status e provisionamento deve mostrar com **êxito**.

![Status da conexão](media/azure-expressroute-connection.png)

Para testar a conexão de rede virtual:

1. Crie uma máquina virtual em sua assinatura do Azure.
2. Localize o endereço IP de seu vCenter de nuvem privada da AVS (consulte seu email de boas-vindas).
3. Execute ping no vCenter da nuvem da máquina virtual criada em sua rede virtual do Azure.
4. Execute ping na máquina virtual do Azure a partir de uma máquina virtual em execução no vCenter privado da sua sincronização do AVS.

Se você tiver problemas para estabelecer a conexão, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.
