---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412939"
---
**Transferências de dados de saída**: as [transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados saindo dos datacenters do Azure) incorrem em cobrança por uso de largura de banda.

**Transações**: Você é cobrado pelo número de transações que você realiza em um disco gerenciado padrão. Para SSDs padrão, cada operação de I/O menor ou igual a 256 KiB de throughput é considerada uma única operação de I/O. Operações de I/O maiores que 256 KiB de throughput são consideradas I/Os múltiplas do tamanho 256 KiB. Para HDDs padrão, cada operação de IO é considerada como uma única transação, independentemente do tamanho de I/O.

Para obter informações detalhadas sobre preços de Discos Gerenciados, incluindo custos de transação, consulte [Preços de Discos Gerenciados](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de disco ultra

As VMs do Azure têm a capacidade de indicar se são compatíveis com ultra discos. Uma VM compatível com um disco ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A inclusão desse recurso na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso de disco ultra na VM sem anexar um disco ultra a ela. Quando um disco ultra é anexado à VM compatível com discos ultra, essa cobrança não é aplicada. Essa cobrança é por vCPU provisionada na VM. 

> [!Note]
> Para [tamanhos de VM principais restritos,](../articles/virtual-machines/linux/constrained-vcpu.md)a taxa de reserva é baseada no número real de vCPUs e não nos núcleos restritos. Para Standard_E32-8s_v3, a taxa de reserva será baseada em 32 núcleos. 

Consulte a [página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes sobre os preços do ultra disco.

### <a name="azure-disk-reservation"></a>Reserva de disco azure

A reserva de disco é a opção de comprar um ano de armazenamento em disco com antecedência com desconto, reduzindo seu custo total. Ao comprar uma reserva de disco, você seleciona um SKU de disco específico em uma região-alvo, por exemplo, 10 SSDs premium P30 (1TiB) na região leste dos EUA 2 por um período de um ano. A experiência de reserva é semelhante às instâncias de máquina virtual reservada (VM). Você pode empacotar reservas de VM e Disco para maximizar suas economias. Por enquanto, a Azure Disks Reservation oferece um plano de compromisso de um ano para SSD SKUs premium de P30 (1TiB) a P80 (32 TiB) em todas as regiões de produção. Para obter mais detalhes sobre os preços dos Discos Reservados, consulte [a página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/).