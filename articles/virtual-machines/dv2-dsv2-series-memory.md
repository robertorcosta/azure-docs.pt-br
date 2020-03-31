---
title: VMs da série Dv2 e DSv2 otimizadas - Azure Virtual Machines
description: Especificações para as VMs da série Dv2 e DSv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914034"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Séries Dv2 e Dsv2 otimizadas de memória

As séries Dv2 e Dsv2, uma continuação da série D original, apresentam uma CPU mais poderosa. Os tamanhos da série DSv2 são executados nos processadores Intel® Xeon® 8171M 2.1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell). A série Dv2 tem as mesmas configurações de memória e disco que a série D.

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

Os tamanhos da série Dv2 são executados nos processadores Intel® Xeon® 8171M 2.1 GHz (Skylake) ou intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250

Armazenamento premium: não suportado

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.
<sup>2</sup> 25000 Mbps com Rede Acelerada.

## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

Os tamanhos da série DSv2 são executados nos processadores Intel® Xeon® 8171M 2.1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira o artigo [Projetar para um alto desempenho](./windows/premium-storage-performance.md).
<sup>2</sup> Instance é isolado para o hardware baseado em Intel Haswell e dedicado a um único cliente.  
<sup>3</sup> Tamanhos limitados de núcleos disponíveis.  
<sup>4</sup> 25000 Mbps com Rede Acelerada.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
