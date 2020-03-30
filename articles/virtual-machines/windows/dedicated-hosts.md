---
title: Visão geral dos Hosts Dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os Hosts Dedicados do Azure podem ser usados para implantar máquinas virtuais.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251562"
---
# <a name="azure-dedicated-hosts"></a>Anfitriões Dedicados azure

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como recurso. Você pode provisionar hosts dedicados dentro de uma região, região de disponibilidade e domínio de falha. Em seguida, você pode colocar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Próximas etapas

- Você pode implantar um host dedicado usando [o Azure PowerShell,](dedicated-hosts-powershell.md)o [portal](dedicated-hosts-portal.md)e [o Azure CLI](../linux/dedicated-hosts-cli.md).

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas quanto domínios de falha para máxima resiliência em uma região.

- Você também pode economizar custos com uma [instância reservada de hosts dedicados do Azure](../prepay-dedicated-hosts-reserved-instances.md).
