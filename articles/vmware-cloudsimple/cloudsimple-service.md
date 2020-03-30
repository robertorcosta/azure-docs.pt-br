---
title: Solução Azure VMware by CloudSimple - Serviço
description: Fornece uma visão geral dos serviços e conceitos do CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024937"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite que você consuma a Solução VMware do CloudSimple.  A criação do serviço permite que você compre nódulos, reserve nós e crie Nuvens Privadas.  Você cria o serviço CloudSimple em cada região do Azure, onde o serviço CloudSimple está disponível. O serviço define a rede de borda do Azure VMware Solution by CloudSimple. A rede de borda suporta serviços que incluem VPN, ExpressRoute e conectividade à internet com suas Nuvens Privadas.

## <a name="gateway-subnet"></a>Gateway de sub-rede

Uma sub-rede gateway é necessária por serviço CloudSimple e é exclusiva da região em que é criada. A sub-rede gateway é usada ao criar a rede de borda e requer um bloco CIDR /28.  O espaço de endereço da sub-rede gateway deve ser único. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e rede virtual Azure.  Uma sub-rede de gateway não pode ser excluída uma vez criada.  A sub-rede gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
