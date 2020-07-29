---
title: Visão geral dos hosts dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os hosts dedicados do Azure podem ser usados para implantar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 15f972f06230e63050f5c4e4b9f3e292d52bd97d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373599"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Hosts dedicados do Azure para máquinas virtuais

O Host Dedicado do Azure é um serviço que fornece servidores físicos - que podem hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, uma zona de disponibilidade e em um domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Próximas etapas

- Você pode implantar um host dedicado usando o [CLI do Azure](dedicated-hosts-cli.md), o [portal](dedicated-hosts-portal.md)e o [PowerShell](../windows/dedicated-hosts-powershell.md).

- Para mais informações, consulte a visão geral de [hosts dedicados](dedicated-hosts.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode economizar em custos com uma [instância reservada de hosts dedicados do Azure](../prepay-dedicated-hosts-reserved-instances.md).
