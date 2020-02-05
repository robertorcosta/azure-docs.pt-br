---
title: Soluções VMware do Azure (AVS)-serviço
description: Fornece uma visão geral do serviço e conceitos do AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024937"
---
# <a name="avs-service-overview"></a>Visão geral do serviço AVS

O serviço AVS permite que você consuma a solução Azure VMware pela AVS. A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas de AVS. Você cria o serviço AVS em cada região do Azure onde o serviço AVS está disponível. O serviço define a rede de borda da solução do Azure VMware por AVS. A rede de borda dá suporte a serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas de AVS.

## <a name="gateway-subnet"></a>Gateway de sub-rede

Uma sub-rede de gateway é necessária por serviço AVS e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente da AVS. As redes que se comunicam com o AVS incluem redes locais e rede virtual do Azure. Uma sub-rede de gateway não pode ser excluída depois de ser criada. A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar um serviço AVS no Azure](quickstart-create-cloudsimple-service.md).
