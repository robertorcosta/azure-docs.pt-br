---
title: Metas de escalabilidade para contas de armazenamento blob de página premium
titleSuffix: Azure Storage
description: Uma conta de armazenamento blob de página de desempenho premium é otimizada para operações de leitura/gravação. Esse tipo de conta de armazenamento ressua um disco não gerenciado para uma máquina virtual do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756242"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Metas de escalabilidade e desempenho para contas de armazenamento blob de página premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Dimensione metas para contas de blob de página premium

Uma conta de armazenamento blob de página de desempenho premium é otimizada para operações de leitura/gravação. Esse tipo de conta de armazenamento ressua um disco não gerenciado para uma máquina virtual do Azure.

> [!NOTE]
> A Microsoft recomenda o uso de discos gerenciados com máquinas virtuais (VMs) do Azure, se possível. Para obter mais informações sobre discos gerenciados, consulte [a visão geral do Armazenamento de Discos do Azure para VMs do Windows](../../virtual-machines/windows/managed-disks-overview.md).

As contas de armazenamento blob de página premium têm os seguintes alvos de escalabilidade:

| Capacidade total da conta                            | Largura de banda total para uma conta de armazenamento com redundância local                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 4 TB (disco individual)/ 35 TB (total acumulado de todos os discos) <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Uma conta blob de página premium é uma conta de uso geral configurada para desempenho premium. As contas de armazenamento v2 de uso geral são recomendadas.

Se você estiver usando contas de armazenamento blob de página premium para discos não gerenciados e seu aplicativo exceder as metas de escalabilidade de uma única conta de armazenamento, então a Microsoft recomenda migrar para discos gerenciados. Para obter mais informações sobre discos gerenciados, consulte [a visão geral do Armazenamento de Discos do Azure para VMs do Windows](../../virtual-machines/windows/managed-disks-overview.md) ou a visão geral do Armazenamento de Discos do [Azure para VMs Linux](../../virtual-machines/linux/managed-disks-overview.md).

Se você não puder migrar para discos gerenciados, crie seu aplicativo para usar várias contas de armazenamento e particione seus dados nessas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, separe-as entre duas contas de armazenamento. O limite para uma conta de armazenamento único premium é de 35 TB. Certifique-se de que uma única conta de armazenamento de desempenho premium nunca tenha mais de 35 TB de discos provisionados.

## <a name="see-also"></a>Confira também

- [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md)
- [Metas de escalabilidade para contas de armazenamento de blob de bloco premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Limites e cotas de assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
