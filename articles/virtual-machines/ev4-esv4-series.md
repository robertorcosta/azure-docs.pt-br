---
title: Ev4 e série Esv4-máquinas virtuais do Azure
description: Especificações para as VMs Ev4 e série Esv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: b8a6e1013c131778bacb11bd885b9280fb6c6077
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182166"
---
# <a name="ev4-and-esv4-series"></a>Séries Ev4 e Esv4

As Ev4 e a série Esv4 são executadas nos &reg; processadores Intel Xeon &reg; Platinum 8272CL (cascadey Lake) em uma configuração de hiperthread, são ideais para vários aplicativos empresariais com uso intensivo de memória e recursos até 504GIB de RAM. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz.

> [!NOTE]
> Para perguntas frequentes, consulte  [tamanhos de VM do Azure sem disco temporário local](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Série Ev4

Os tamanhos da série Ev4 são executados no Intel Xeon &reg; Platinum 8272CL (cascadey Lake). As instâncias da série Ev4 são ideais para aplicativos empresariais com uso intensivo de memória. As VMs da série Ev4 apresentam &reg; a tecnologia Intel Hyper-Threading.

O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais. Para usar discos de armazenamento Premium, use os tamanhos de Esv4. Os medidores de cobrança e preço para os tamanhos de Esv4 são os mesmos que os da série Ev4.

ACU: 195 - 210

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Somente armazenamento remoto | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Somente armazenamento remoto | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Somente armazenamento remoto | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Somente armazenamento remoto | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Somente armazenamento remoto | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Somente armazenamento remoto | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Somente armazenamento remoto | 32 | 8|24.000 |
| Standard_E64_v4 | 64 | 504 | Somente armazenamento remoto | 32| 8|30000 |


## <a name="esv4-series"></a>Série Esv4

Os tamanhos da série Esv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (cascadey Lake). As instâncias da série Esv4 são ideais para aplicativos empresariais com uso intensivo de memória. As VMs da série Evs4 apresentam &reg; a tecnologia Intel Hyper-Threading. O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais.

ACU: 195 a 210

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Somente armazenamento remoto | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Somente armazenamento remoto | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Somente armazenamento remoto | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Somente armazenamento remoto | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Somente armazenamento remoto | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Somente armazenamento remoto | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Somente armazenamento remoto | 32 | 76800/1152 | 8|24.000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Somente armazenamento remoto | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [tamanhos de núcleos restritos disponíveis](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
