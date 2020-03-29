---
title: Série Fsv2 - Azure Virtual Machines
description: Especificações para as VMs da série Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164841"
---
# <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 é baseada no processador Intel® Xeon® Platinum 8168. Possui uma velocidade de relógio Turbo de 3,4 GHz e uma frequência turbo de núcleo único de 3,7 GHz. As instruções ® Intel AVX-512 são novas em processadores Intel Scalable. Essas instruções fornecem até um aumento de desempenho 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Em outras palavras, eles são muito rápidos para qualquer carga de trabalho computacional.

As VMs da série FSV2 possuem tecnologia Intel® Hyper-Threading.

ACU: 195 - 210

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | da vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> O uso de mais de 64 vCPU requer um desses sistemas operacionais de hóspedes suportados:

- Windows Server 2016 ou posterior
- Ubuntu 16.04 LTS ou posterior, com kernel afinado do Azure (4.15 kernel ou posterior)
- Less 12 SP2 ou posterior
- Versão 6.7 ou CentOS 6.7 a 6.10, com o pacote LIS 4.3.1 (ou posterior) fornecido pela Microsoft instalado
- Versão 7.3 do RHEL ou CentOS, com o pacote LIS 4.2.1 (ou posterior) fornecido pela Microsoft instalado
- RHEL ou CentOS versão 7.6 ou posterior
- Oracle Linux com UEK4 ou posterior
- Debian 9 com o kernel backports, Debian 10 ou posterior
- CoreOS com um kernel 4.14 ou posterior

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

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
