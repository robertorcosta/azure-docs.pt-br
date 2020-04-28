---
title: 'Armazenamento Premium do Azure: projetado para desempenho em VMs Windows | Microsoft Docs'
description: Crie aplicativos de alto desempenho usando os discos gerenciados do SSD Premium do Azure. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75371319"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento Premium do Azure: projeto para alto desempenho
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um gargalo de rede. Nessas situações, você deve otimizar seu [desempenho de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).
>
> Se sua VM oferecer suporte a rede acelerada, verifique se ela está ativada. Se não estiver ativado, você poderá ativá-lo em VMs já implementadas nos [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você for novo no armazenamento Premium, leia primeiro o [tipo de disco do Azure para VMs de IaaS](disks-types.md) e [metas de escalabilidade para contas de armazenamento de blob de página Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo de disco](disks-types.md)  

Para usuários do SQL Server, leia os artigos sobre Práticas recomendadas de desempenho para o SQL Server:

* [Práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](sql/virtual-machines-windows-sql-performance.md)
* [Armazenamento Premium do Azure fornece desempenho mais alto para SQL Server na VM do Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)