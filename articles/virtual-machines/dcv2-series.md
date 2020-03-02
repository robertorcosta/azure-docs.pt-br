---
title: Série DC-máquinas virtuais do Azure
description: Especificações para as VMs da série DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: fd8f01f0fc7180d271404ffee4496ff9cbac7222
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205918"
---
# <a name="preview-dcv2-series"></a>Versão prévia: série DCv2


A série DCv2 pode ajudar a proteger a confidencialidade e a integridade de seus dados e código enquanto eles são processados na nuvem pública. Essas máquinas têm o respaldo da última geração de processador E-2288G Intel XEON com tecnologia de SGX. Com a tecnologia Intel Turbo Boost, essas máquinas podem ir até 5.0 GHz. As instâncias da série DCv2 permitem que os clientes criem aplicativos baseados em enclave seguros para proteger seu código e seus dados enquanto eles estão em uso.

Exemplos de casos de uso incluem compartilhamento de dados multipartes confidenciais, detecção de fraudes, Laundering de blockchain, análise de uso confidencial, análise de inteligência e aprendizado de máquina confidencial.

Armazenamento Premium: com suporte *

Cache de armazenamento Premium: com suporte *

Migração ao Vivo: sem suporte

Atualizações de preservação de memória: sem suporte

\* Exceto para Standard_DC8_v2



| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/largura de banda de rede esperada (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- As VMs da série DCv2 são [VMs de geração 2](./linux/generation-2.md#creating-a-generation-2-vm) e só oferecem suporte a imagens `Gen2`.
- Atualmente disponível somente no Sul do Reino Unido.
- Geração anterior de VMs de computação confidencial: [série de DC](sizes-previous-gen.md)
- Criar VMs DCv2 usando o portal do Azure [criar VM-portal](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
