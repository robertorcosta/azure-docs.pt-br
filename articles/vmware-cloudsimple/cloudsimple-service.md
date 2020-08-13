---
title: Solução do Azure VMware por CloudSimple-Service
description: Saiba mais sobre o serviço CloudSimple com sua visão geral. A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142121"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite que você consuma a solução do Azure VMware pelo CloudSimple.  A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.  Você cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de borda da solução do Azure VMware por CloudSimple. A rede de borda dá suporte a serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas.

## <a name="gateway-subnet"></a>Sub-rede do gateway

Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28.  O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e rede virtual do Azure.  Uma sub-rede de gateway não pode ser excluída depois de ser criada.  A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
