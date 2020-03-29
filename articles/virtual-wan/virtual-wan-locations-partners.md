---
title: Parceiros e locais de WAN virtuais do Azure | Microsoft Docs
description: Este artigo contém uma lista de parceiros WAN virtuais do Azure e localizações do hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123278"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros WAN Virtual e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros suportados pela WAN virtual para conectividade no Virtual Hub.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure. A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos do provedor através de um parceiro WAN virtual. O uso de dispositivos parceiros permite facilidade de uso, simplificação da conectividade e gerenciamento de configuração.

A conectividade do dispositivo local é estabelecida em uma forma automatizada para o Hub Virtual. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). Você só pode ter um hub por região.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automação de parceiros de conectividade

Dispositivos que se conectam à WAN Virtual do Azure têm automação interna para conexão. Normalmente, isso é definido na interface do usuário de gerenciamento do dispositivo (ou equivalente), que configura a conectividade e o gerenciamento de configuração entre o dispositivo de branch VPN para um ponto de extremidade de VPN de Hub Virtual do Azure (gateway de VPN).

A automação de alto nível seguinte é configurada no centro de gerenciamento/console do dispositivo:

* Permissões apropriadas para o dispositivo para acessar o grupo de recursos da WAN Virtual do Azure
* Carregamento de dispositivo de branch na WAN Virtual do Azure
* Download automático de informações de conectividade do Azure
* Configuração de dispositivo de branch local 

Alguns parceiros de conectividade podem estender a automação para incluir a criação do Gateway de VPN e VNet do Hub Virtual do Azure. Se você quiser saber mais sobre automação, consulte [as diretrizes de automação para parceiros de WAN virtuais.](virtual-wan-configure-automation-providers.md)

## <a name="connectivity-through-partners"></a><a name="partners"></a>Conectividade através de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os seguintes parceiros estão programados em nosso roteiro para um futuro próximo: 128 Tecnologias, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink e VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com o Azure Virtual WAN, consulte [as diretrizes de automação para parceiros WAN virtuais](virtual-wan-configure-automation-providers.md).
