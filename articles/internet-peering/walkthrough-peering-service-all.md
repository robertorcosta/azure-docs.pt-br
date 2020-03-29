---
title: Passo a passo do parceiro de Serviço de Emparelhamento
titleSuffix: Azure
description: Passo a passo do parceiro de Serviço de Emparelhamento
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720241"
---
# <a name="peering-service-partner-walkthrough"></a>Passo a passo do parceiro de Serviço de Emparelhamento

Esta seção explica as etapas que um provedor precisa seguir para habilitar um peering direct para peering service.

## <a name="create-direct-peering-connection-for-peering-service"></a>Crie conexão de peering direta para serviço de peering
Os provedores de serviços podem expandir seu alcance geográfico criando novos peering diretos que suportam o Peering Service. Para conseguir isso,
1. Torne-se um parceiro de serviço de peering se ainda não
1. Siga as instruções para [criar ou modificar um peering direto usando o portal](howto-direct-portal.md). Certifique-se de atender aos requisitos de alta disponibilidade.
1. Em seguida, siga as etapas para [ativar o serviço de peering em um peering direto usando o portal](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Use a conexão de peering direta legado para o serviço de peering
Se você tiver um peering direct legado que você deseja usar para suportar o peering Service,
1. Torne-se um parceiro de peering service, se não já.
1. Siga as instruções para [converter um recurso direct legacy para o recurso Azure usando o portal](howto-legacy-direct-portal.md). Se necessário, solicite circuitos adicionais para atender aos requisitos de alta disponibilidade.
1. Em seguida, siga as etapas para [ativar o serviço de peering em um peering direto usando o portal](howto-peering-service-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a política de peering](https://peering.azurewebsites.net/peering).
* Para saber sobre os passos para configurar o peering direto com a Microsoft, siga [o passo a passo do Direct .](walkthrough-direct-all.md)
* Para saber sobre os passos para configurar o peering do Exchange com a Microsoft, siga [o passo a passo do Exchange](walkthrough-exchange-all.md).