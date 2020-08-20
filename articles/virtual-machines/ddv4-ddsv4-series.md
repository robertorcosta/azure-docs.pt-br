---
title: Séries Ddv4 e Ddsv4
description: Especificações das VMs séries Dv4, Ddv4, Dsv4 e Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 3c51a00e5760a2dac1648cc938416625bd52ddb7
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649900"
---
# <a name="ddv4-and-ddsv4-series"></a>Séries Ddv4 e Ddsv4

As séries Ddv4 e Ddsv4 são executadas nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) em uma configuração hyper-threaded, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral. Elas apresentam uma velocidade de clock Turbo sustentável em todos os núcleos de 3,4 GHz, com Tecnologia [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Tecnologia Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Eles também dão suporte ao [ &reg; aumento de aprendizado profundo da Intel](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Esses novos tamanhos de VM terão um armazenamento local 50% maior, bem como um melhor IOPS de disco local para leitura e gravação em comparação com os tamanhos [Dv3/Dsv3](./dv3-dsv3-series.md) com [VMs Gen2](./linux/generation-2.md).

Os casos de uso da série D incluem aplicativos de nível empresarial, bancos de dados relacionais, cache em memória e análises.

## <a name="ddv4-series"></a>Série Ddv4

Os tamanhos da série Ddv4 são executados no Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). A série Ddv4 oferece uma combinação de vCPU, memória e disco temporário para a maioria das cargas de trabalho de produção.

Os novos tamanhos de VM Ddv4 incluem um armazenamento SSD local mais rápido e maior (até 2.400 GiB) e são projetados para aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência, como aplicativos que exigem leituras/gravações rápidas em armazenamento temporário ou que precisam de armazenamento temporário para caches ou arquivos temporários. Você pode anexar armazenamento de SSDs Standard e HDDs Standard às VMs Ddv4. O armazenamento em disco de dados remotos é cobrado separadamente das máquinas virtuais.

ACU: 195 a 210

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24.000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

## <a name="ddsv4-series"></a>Série Ddsv4

A série Ddsv4 é executada no Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). A série Ddsv4 oferece uma combinação de vCPU, memória e disco temporário para a maioria das cargas de trabalho de produção.

Os novos tamanhos de VM Ddsv4 incluem um armazenamento SSD local mais rápido e maior (até 2.400 GiB) e são projetados para aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência, como aplicativos que exigem leituras/gravações rápidas em armazenamento temporário ou que precisam de armazenamento temporário para caches ou arquivos temporários. 

 > [!NOTE]
 >Os medidores de cobrança e preço para os tamanhos da Ddsv4 são os mesmos que os da série Ddv4.

ACU: 195 a 210

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Com suporte

Atualizações de preservação de memória: Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24.000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

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
