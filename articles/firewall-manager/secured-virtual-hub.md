---
title: O que é um hub virtual seguro?
description: Saiba mais sobre hubs virtuais seguros
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518431"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Um hub virtual é uma rede virtual gerenciada pela Microsoft que permite a conectividade de outros recursos. Quando um hub virtual é criado a partir de uma WAN virtual no portal Azure, um hub virtual VNet e gateways (opcionais) são criados como seus componentes.

Um hub virtual *protegido* é um [Hub WAN Virtual do Azure](../virtual-wan/virtual-wan-about.md#resources) com políticas de segurança e roteamento associadas configuradas pelo Azure Firewall Manager. Use hubs virtuais protegidos para criar facilmente arquiteturas de hub-and-spoke e transitivas com serviços de segurança nativa para governança e proteção de tráfego. 

Você pode usar um hub virtual protegido como um VNet central gerenciado sem conectividade on-prem. Ele substitui o VNet central que anteriormente era necessário para uma implantação do Azure Firewall. Como o hub virtual protegido fornece roteamento automatizado, não há necessidade de configurar seus próprios UDRs (rotas definidas pelo usuário) para direcionar o tráfego através do firewall.

Também é possível usar hubs virtuais protegidos como parte de uma arquitetura WAN virtual completa. Essa arquitetura fornece conectividade de ramificações seguras, otimizadas e automatizadas para e através do Azure. Você pode escolher os serviços para proteger e controlar o tráfego de sua rede, incluindo o Firewall Azure e outros provedores de segurança de terceiros como um serviço (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Crie um hub virtual protegido

Usando o Firewall Manager no portal Azure, você pode criar um novo hub virtual protegido ou converter um hub virtual existente que você criou anteriormente usando o Azure Virtual WAN.

## <a name="next-steps"></a>Próximas etapas

Para criar um hub virtual protegido e usá-lo para proteger e governar uma rede de hub e spoke, consulte [Tutorial: Proteja sua rede na nuvem com o Azure Firewall Manager usando o portal Azure](secure-cloud-network.md).