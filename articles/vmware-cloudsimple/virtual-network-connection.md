---
title: Conecte a rede virtual Do Zure ao CloudSimple usando a Solução VMware Do ExpressRoute - Azure pela CloudSimple
description: Descreve como obter informações de peering para uma conexão entre a rede virtual do Azure e seu ambiente CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563998"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Conecte a rede virtual do Azure ao CloudSimple usando o ExpressRoute

Você pode estender sua rede Private Cloud para sua rede virtual Azure e recursos do Azure. Uma conexão ExpressRoute permite que você acesse recursos em execução em sua assinatura do Azure a partir de sua Nuvem Privada.

## <a name="request-authorization-key"></a>Chave de autorização de solicitação

Uma chave de autorização é necessária para a conexão ExpressRoute entre sua Nuvem Privada e a rede virtual Do Zure. Para obter uma chave, arquive um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a>.  Use as seguintes informações na solicitação:

* Tipo de emissão: **Técnico**
* Assinatura: **Selecione a assinatura onde o serviço CloudSimple é implantado**
* Serviço: **Solução VMware pela CloudSimple**
* Tipo de problema: **Solicitação de serviço**
* Subtipo do problema: **Chave de autorização para conexão Azure VNET**
* Assunto: **Solicitação de chave de autorização para conexão Azure VNET**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Obtenha informações de peering do portal CloudSimple

Para configurar a conexão, você deve estabelecer uma conexão entre a rede virtual Do Zure e seu ambiente CloudSimple.  Como parte do procedimento, você deve fornecer o URI do circuito de pares e a chave de autorização. Obtenha o URI e a chave de autorização no [portal CloudSimple](access-cloudsimple-portal.md).  Selecione **Rede** no menu lateral e selecione **Conexão de rede Azure**. Ou selecione **Conta** no menu lateral e selecione **conexão de rede Azure**.

Copiar uri circuito de pares e para a chave de autorização para cada uma das regiões usando ícone *de cópia.* Para cada região do CloudSimple que você deseja conectar:

1. Clique **em Copiar** para copiar o URI. Cole-o em um arquivo onde ele pode estar disponível para adicionar ao portal Azure.  
2. Clique **em Copiar** para copiar a chave de autorização e cole-a no arquivo também.

Copie a chave de autorização e o uri do circuito de pares que está no estado **disponível.**  **O** status usado indica que a chave já foi usada para criar uma conexão de rede virtual.

![Página de Conexão de Rede Virtual](media/virtual-network-connection.png)

Para obter detalhes sobre a configuração da rede virtual Do Zure para o link CloudSimple, consulte [Conecte seu ambiente CloudSimple Private Cloud à rede virtual Azure usando o ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Próximas etapas

* [Conexão de rede virtual do Azure com a Private Cloud](azure-expressroute-connection.md)
* [Conecte-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
