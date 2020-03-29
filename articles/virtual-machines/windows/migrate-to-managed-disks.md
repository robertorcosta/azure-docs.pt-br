---
title: Migrar VMs do Azure para discos gerenciados
description: Migre máquinas virtuais do Azure criadas com discos não gerenciados em contas de armazenamento a fim de usar o Managed Disks.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921344"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para o Managed Disks no Azure

O Azure Managed Disks simplifica o gerenciamento do armazenamento, acabando com a necessidade de gerenciar as contas de armazenamento de forma separada.  Também é possível migrar suas VMs do Azure para o Managed Disks para aproveitar a melhor confiabilidade das VMs em um conjunto de disponibilidade. Ele garante que os discos de vMs diferentes em um conjunto de disponibilidade sao suficientemente isolados entre si para evitar um único ponto de falhas. Ele coloca automaticamente os discos de VMs diferentes em um conjunto de disponibilidade em unidades de escala (carimbos) de armazenamentos diferentes, o que limita o impacto de falhas em uma única unidade de escala de armazenamento causadas por falhas de hardware e de software.
Com base em suas necessidades, você pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, confira nosso artigo [Selecionar um tipo de disco](disks-types.md)

## <a name="migration-scenarios"></a>Cenários de migração

É possível migrar para o Managed Disks nos cenários a seguir:

|Cenário  |Artigo  |
|---------|---------|
|Converter VMs autônomas e VMs em um conjunto de disponibilidade para discos gerenciados     |[Converter VMs para usar discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Converta uma única VM de classic a Resource Manager em discos gerenciados     |[Criar uma VM de um VHD clássico](create-vm-specialized-portal.md)         |
|Converta todas as VMs em um vNet de classic para Resource Manager em discos gerenciados     |[Migrar os recursos de IaaS do modo clássico para o modo do Resource Manager](migration-classic-resource-manager-ps.md) e [Converter uma VM de discos não gerenciados para discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Atualize as VMs com discos padrão não gerenciados para VMs com discos premium gerenciados     | Primeiro, [converta uma máquina virtual do Windows de discos não gerenciados para discos gerenciados](convert-unmanaged-to-managed-disks.md). Em [seguida, atualize o tipo de armazenamento de um disco gerenciado](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [discos gerenciados](managed-disks-overview.md)
- Confira os [preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
