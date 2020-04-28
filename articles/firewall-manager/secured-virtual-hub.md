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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518431"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Um hub virtual é uma rede virtual gerenciada pela Microsoft que permite a conectividade de outros recursos. Quando um hub virtual é criado de uma WAN virtual no portal do Azure, uma VNet de Hub virtual e gateways (opcional) são criados como seus componentes.

Um hub virtual *protegido* é um [Hub de WAN virtual do Azure](../virtual-wan/virtual-wan-about.md#resources) com políticas de roteamento e segurança associadas configuradas pelo Gerenciador de firewall do Azure. Use hubs virtuais protegidos para criar facilmente arquiteturas de Hub e spoke e transitivas com serviços de segurança nativos para governança e proteção de tráfego. 

Você pode usar um hub virtual seguro como uma VNet central gerenciada sem conectividade local. Ele substitui a VNet central que antes era necessária para uma implantação de firewall do Azure. Como o Hub virtual protegido fornece roteamento automatizado, não há necessidade de configurar seu próprio UDRs (rotas definidas pelo usuário) para rotear o tráfego pelo firewall.

Também é possível usar hubs virtuais protegidos como parte de uma arquitetura WAN virtual completa. Essa arquitetura fornece conectividade de ramificação segura, otimizada e automatizada para e por meio do Azure. Você pode escolher os serviços para proteger e controlar o tráfego de rede, incluindo o Firewall do Azure e outros provedores de SECaaS (segurança como serviço) de terceiros.

## <a name="create-a-secured-virtual-hub"></a>Criar um hub virtual protegido

Usando o Gerenciador de firewall no portal do Azure, você pode criar um novo hub virtual protegido ou converter um hub virtual existente que você criou anteriormente usando a WAN virtual do Azure.

## <a name="next-steps"></a>Próximas etapas

Para criar um hub virtual seguro e usá-lo para proteger e controlar uma rede de Hub e spoke, consulte [tutorial: proteger sua rede de nuvem com o Gerenciador de firewall do Azure usando o portal do Azure](secure-cloud-network.md).