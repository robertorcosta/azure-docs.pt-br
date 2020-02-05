---
title: Conectar a rede virtual do Azure à AVS usando o ExpressRoute
description: Descreve como obter informações de emparelhamento para uma conexão entre a rede virtual do Azure e seu ambiente de AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014395"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Conectar a rede virtual do Azure à AVS usando o ExpressRoute

Você pode estender sua rede de nuvem privada de AVS para sua rede virtual do Azure e recursos do Azure. Uma conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure da sua nuvem privada de AVS.

## <a name="request-authorization-key"></a>Solicitar chave de autorização

Uma chave de autorização é necessária para a conexão de ExpressRoute entre sua nuvem privada de AVS e a rede virtual do Azure. Para obter uma chave, arquivo de um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a>. Use as seguintes informações na solicitação:

* Tipo de problema: **técnico**
* Assinatura: **Selecione a assinatura na qual o serviço de sincronização automática está implantado**
* Serviço: **soluções VMware (AVS)**
* Tipo de problema: **solicitação de serviço**
* Subtipo de problema: **chave de autorização para conexão de VNET do Azure**
* Assunto: **solicitação de chave de autorização para conexão de VNET do Azure**

## <a name="get-peering-information-from-avs-portal"></a>Obter informações de emparelhamento do portal da AVS

Para configurar a conexão, você deve estabelecer uma conexão entre a rede virtual do Azure e o ambiente da AVS. Como parte do procedimento, você deve fornecer o URI do circuito par e a chave de autorização. Obtenha o URI e a chave de autorização do [portal da AVS](access-cloudsimple-portal.md). Selecione **rede** no menu lateral e, em seguida, selecione **conexão de rede do Azure**. Ou selecione **conta** no menu lateral e, em seguida, selecione **conexão de rede do Azure**.

Copie o URI do circuito par e a chave de autorização para cada uma das regiões usando o ícone de *cópia* . Para cada região da AVS que você deseja conectar:

1. Clique em **copiar** para copiar o URI. Cole-o em um arquivo no qual ele pode estar disponível para ser adicionado ao portal do Azure. 
2. Clique em **copiar** para copiar a chave de autorização e cole-a no arquivo também.

Copie a chave de autorização e o URI do circuito par que está no estado **disponível** . Status **usado** indica que a chave já foi usada para criar uma conexão de rede virtual.

![Página conexão de rede virtual](media/virtual-network-connection.png)

Para obter detalhes sobre como configurar a rede virtual do Azure para o link da AVS, consulte [conectar seu ambiente de nuvem privada da AVS à rede virtual do Azure usando o ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Próximos passos

* [Conexão de rede virtual do Azure para a nuvem privada de AVS](azure-expressroute-connection.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
