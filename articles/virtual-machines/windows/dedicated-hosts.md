---
title: Visão geral dos hosts dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os hosts dedicados do Azure podem ser usados para implantar máquinas virtuais.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82082825"
---
# <a name="azure-dedicated-hosts"></a>Hosts dedicados do Azure

O Host Dedicado do Azure é um serviço que fornece servidores físicos - que podem hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, uma zona de disponibilidade e em um domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Próximas etapas

- Você pode implantar um host dedicado usando [Azure PowerShell](dedicated-hosts-powershell.md), o [portal](dedicated-hosts-portal.md)e o [CLI do Azure](../linux/dedicated-hosts-cli.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode economizar em custos com uma [instância reservada de hosts dedicados do Azure](../prepay-dedicated-hosts-reserved-instances.md).
