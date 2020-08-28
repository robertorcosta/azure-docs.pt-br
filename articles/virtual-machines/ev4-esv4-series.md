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
ms.openlocfilehash: 6e35e32c92535a408c8df22d7306895150a59519
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050141"
---
# <a name="ev4-and-esv4-series"></a>Séries Ev4 e Esv4

As Ev4 e a série Esv4 são executadas nos &reg; processadores Intel Xeon &reg; Platinum 8272CL (cascadey Lake) em uma configuração de hiperthread, são ideais para vários aplicativos empresariais com uso intensivo de memória e recursos até 504GIB de RAM. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz.

> [!NOTE]
> Para perguntas frequentes, consulte  [tamanhos de VM do Azure sem disco temporário local](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Série Ev4

Os tamanhos da série Ev4 são executados no Intel Xeon &reg; Platinum 8272CL (cascadey Lake). As instâncias da série Ev4 são ideais para aplicativos empresariais com uso intensivo de memória. As VMs da série Ev4 apresentam &reg; a tecnologia Intel Hyper-Threading.

O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais. Para usar discos de armazenamento Premium, use os tamanhos de Esv4. Os medidores de cobrança e preço para os tamanhos de Esv4 são os mesmos que os da série Ev4.

> [!IMPORTANT]
> Esses novos tamanhos estão atualmente em visualização pública apenas. Você pode inscrever-se para essas Ev4 e a série Esv4 [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

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

> [!IMPORTANT]
> Esses novos tamanhos estão atualmente em visualização pública apenas. Você pode inscrever-se para essas Ev4 e a série Esv4 [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195 a 210

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima em cache: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Somente armazenamento remoto | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Somente armazenamento remoto | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Somente armazenamento remoto | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Somente armazenamento remoto | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Somente armazenamento remoto | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Somente armazenamento remoto | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Somente armazenamento remoto | 32 | 462000/2904 (1200) | 76800/1152 | 8|24.000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Somente armazenamento remoto | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

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
