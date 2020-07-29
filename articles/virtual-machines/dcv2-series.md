---
title: Série DC – Máquinas Virtuais do Azure
description: Especificações para as VMs da série DC.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: c5074a81625f38cf94d5acd9751414bad4a9c3e8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292455"
---
# <a name="dcsv2-series"></a>Série DCsv2


A série DCsv2 pode ajudar a proteger a confidencialidade e a integridade dos dados e do código enquanto eles são processados na nuvem pública. Esses computadores têm o suporte da última geração do processador Intel XEON E-2288G com a tecnologia SGX. Com a Tecnologia Intel Turbo Boost, esses computadores podem chegar a 5,0 GHz. As instâncias da série DCsv2 permitem que os clientes criem aplicativos seguros baseados em enclave para proteger o código e os dados enquanto estiverem em uso.

Exemplos de casos de uso incluem: compartilhamento de dados confidenciais entre vários participantes, detecção de fraudes, antilavagem de dinheiro, blockchain, análise de uso confidencial, análise de inteligência e machine learning confidencial.

Armazenamento Premium: Com suporte*

Cache de Armazenamento Premium: Com suporte*

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

*Exceto para Standard_DC8_v2



| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Número máximo de NICs/largura de banda de rede esperada (MBps) | Memória EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2\.000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4\.000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8\.000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16.000/128                                                                                         | 2   | 168                                         |

- As VMs da série DCsv2 são [VMs de geração 2](./linux/generation-2.md#creating-a-generation-2-vm) e dão suporte apenas a imagens `Gen2`.
- Disponível atualmente nas regiões listadas [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).
- Geração anterior de VMs de computação confidencial: [Série DC](sizes-previous-gen.md#preview-dc-series)
- Criar VMs DCsv2 usando o [portal do Azure](./linux/quick-create-portal.md) ou o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
