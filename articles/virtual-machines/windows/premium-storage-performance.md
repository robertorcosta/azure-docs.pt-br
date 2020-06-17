---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Microsoft Docs'
description: Crie aplicativos de alto desempenho usando os Managed Disks SSD Premium do Azure. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 06ac680ef48d9602f6e7487eb9ebecee9f3ae080
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017589"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento Premium do Azure: projeto para alto desempenho
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um gargalo de rede. Nessas situações, você deve otimizar seu [desempenho de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você busca submeter a benchmark seu disco, confira nosso artigo sobre [Como avaliar o desempenho de um disco](disks-benchmarks.md).
>
> Se sua VM oferecer suporte a rede acelerada, verifique se ela está ativada. Se não estiver ativado, você poderá ativá-lo em VMs já implementadas nos [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você não estiver familiarizado com o Armazenamento Premium, leia primeiro os artigos [Selecionar um tipo de disco do Azure para VMs IaaS](disks-types.md) e [Metas de escalabilidade para contas de Armazenamento de Blobs de Páginas Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se você busca submeter a benchmark seu disco, confira nosso artigo sobre [Como avaliar o desempenho de um disco](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo de disco](disks-types.md)  

Para usuários do SQL Server, leia os artigos sobre Práticas recomendadas de desempenho para o SQL Server:

* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Armazenamento Premium do Azure fornece desempenho mais alto para SQL Server na VM do Azure](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
