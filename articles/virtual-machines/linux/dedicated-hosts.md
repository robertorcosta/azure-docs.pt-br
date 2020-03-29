---
title: Visão geral dos Hosts Dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os Hosts Dedicados do Azure podem ser usados para implantar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970739"
---
# <a name="azure-dedicated-hosts"></a>Anfitriões Dedicados azure

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como recurso. Você pode provisionar hosts dedicados dentro de uma região, região de disponibilidade e domínio de falha. Em seguida, você pode colocar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Próximas etapas

- Você pode implantar um host dedicado usando o [Azure CLI](dedicated-hosts-cli.md), [portal](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Para obter mais informações, consulte a visão geral dos [hosts dedicados.](dedicated-hosts.md)

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas quanto domínios de falha para máxima resiliência em uma região.

- Você também pode economizar custos com uma [instância reservada de hosts dedicados do Azure](../prepay-dedicated-hosts-reserved-instances.md).
