---
title: Série DC - Azure Virtual Machines
description: Especificações para as VMs da série DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085715"
---
# <a name="dcsv2-series"></a>Série DCsv2


A série DCsv2 pode ajudar a proteger a confidencialidade e integridade de seus dados e códigos enquanto são processados na nuvem pública. Estas máquinas são apoiadas pela última geração do Processador Intel XEON E-2288G com tecnologia SGX. Com a tecnologia Intel Turbo Boost essas máquinas podem ir até 5.0GHz. As instâncias da série DCsv2 permitem que os clientes construam aplicativos seguros baseados em enclave para proteger seu código e dados enquanto eles estão em uso.

Exemplos de casos de uso incluem: compartilhamento de dados multipartidários confidenciais, detecção de fraudes, lavagem de dinheiro, blockchain, análise de uso confidencial, análise de inteligência e aprendizado de máquina confidencial.

Armazenamento premium: suportado*

Cache de armazenamento premium: suportado*

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

* Exceto para Standard_DC8_v2



| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo NICs / Largura de banda de rede esperada (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- VMs da série DCsv2 são [VMs de geração 2](./linux/generation-2.md#creating-a-generation-2-vm) e apenas imagens de suporte. `Gen2`
- Atualmente disponível apenas no Sul do Reino Unido, Canadá Central e LESTE dos EUA.
- Geração anterior de VMs de Computação Confidencial: [Série DC](sizes-previous-gen.md#preview-dc-series)
- Crie VMs DCsv2 usando o [portal Azure](./linux/quick-create-portal.md) ou [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
