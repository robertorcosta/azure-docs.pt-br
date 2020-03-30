---
title: Crie pools de recursos com mapeamento de assinaturas do Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como criar pools de recursos para sua Nuvem Privada através do mapeamento de assinaturas do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014956"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Crie pools de recursos para sua Nuvem Privada com mapeamento de assinaturas do Azure
O mapeamento de assinaturas do Azure permite criar pools de recursos para sua Nuvem Privada a partir dos pools de recursos disponíveis do vSphere. No portal CloudSimple, você pode visualizar e gerenciar a assinatura do Azure para suas Nuvens Privadas.

> [!NOTE]
> O mapeamento de um pool de recursos também mapeia quaisquer pools de recursos infantis. Um pool de recursos dos pais não pode ser mapeado se algum pool de recursos filho já estiver mapeado.

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md).
2. Abra a página **Recursos** e selecione **o mapeamento de assinaturas do Azure**.  
3. Clique **em Editar mapeamento de assinatura do Azure**.  
4. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta virada para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta voltada para a esquerda. 

    ![Assinaturas do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
