---
title: Localizações dos parceiros WAN Virtual do Azure | Microsoft Docs
description: Este artigo contém uma lista de parceiros de WAN virtual do Azure e locais de Hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 985823e5aa806244596277c982cc129554d890f2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489061"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros WAN Virtual e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros virtuais com suporte da WAN para conectividade com o Hub virtual.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure. A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos de provedor por meio de um parceiro de WAN virtual. O uso de dispositivos de parceiros permite facilitar o uso, simplificar a conectividade e o gerenciamento de configuração.

A conectividade do dispositivo local é estabelecida em uma forma automatizada para o Hub Virtual. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). Você só pode ter um hub por região.

## <a name="automation"></a>Automação de parceiros de conectividade

Dispositivos que se conectam à WAN Virtual do Azure têm automação interna para conexão. Normalmente, isso é definido na interface do usuário de gerenciamento do dispositivo (ou equivalente), que configura a conectividade e o gerenciamento de configuração entre o dispositivo de branch VPN para um ponto de extremidade de VPN de Hub Virtual do Azure (gateway de VPN).

A automação de alto nível seguinte é configurada no centro de gerenciamento/console do dispositivo:

* Permissões apropriadas para o dispositivo para acessar o grupo de recursos da WAN Virtual do Azure
* Carregamento de dispositivo de branch na WAN Virtual do Azure
* Download automático de informações de conectividade do Azure
* Configuração de dispositivo de branch local 

Alguns parceiros de conectividade podem estender a automação para incluir a criação do Gateway de VPN e VNet do Hub Virtual do Azure. Se você quiser saber mais sobre a automação, consulte [Configurar a automação – Parceiros WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Conectividade por meio de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os parceiros a seguir são candidatos a nosso roteiro para o futuro próximo: Arista, F5 Networks, Velocloud.

## <a name="locations"></a>Localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com a WAN Virtual do Azure, consulte [Parceiros da WAN Virtual – Como automatizar](virtual-wan-configure-automation-providers.md).
