---
title: Tamanhos Azure VM - GPU | Microsoft Docs
description: Lista os diferentes tamanhos otimizados de GPU disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913575"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas

Os tamanhos de VM otimizados para GPU são máquinas virtuais especializadas disponíveis com um ou vários GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho de visualização e com muita computação e muitos gráficos. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento.

- [Os](nc-series.md)tamanhos da série [NC, série NCv2,](ncv2-series.md) [Série NCv3](ncv3-series.md) são otimizados para aplicações e algoritmos intensivos em computação e com uso intensivo de rede. Alguns exemplos são aplicativos e simulações baseadas em CUDA e OpenCL, IA e Deep Learning. A série NCv3 concentra-se em cargas de trabalho de computação de alto desempenho com a tecnologia das GPUs Tesla V100 da NVIDIA. A série NC usa o processador Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell), e as VMs da série NCv2 e NCv3 usam o processador Intel Xeon E5-2690 v4 (Broadwell).

- Os tamanhos [das séries ND](nd-series.md)e [NDv2](ndv2-series.md) são focados em cenários de treinamento e inferência para o aprendizado profundo. Eles usam a GPU NVIDIA Tesla P40 e o processador Intel Xeon E5-2690 v4 (Broadwell). A série NDv2 usa o processador Intel Xeon Platinum 8168 (Skylake).

- [Os](nv-series.md) tamanhos das séries NV e [NVv3](nvv3-series.md) são otimizados e projetados para visualização remota, streaming, jogos, codificação e cenários VDI usando frameworks como OpenGL e DirectX. Essas VMs são apoiadas pela GPU Tesla M60 da NVIDIA.

- [Série NVV4](nvv4-series.md) Tamanhos vm otimizados e projetados para VDI e visualização remota. Com GPUs particionadas, o NVv4 oferece o tamanho certo para cargas de trabalho que requerem recursos menores de GPU. Estes VMs são apoiados pela GPU AMD Radeon Instinct MI25. As VMs NVv4 atualmente suportam apenas o sistema operacional convidado do Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série Azure N, os drivers de GPU NVIDIA devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](/azure/virtual-machines/extensions/overview).

Se você optar por instalar os drivers de GPU NVIDIA manualmente, consulte [a configuração do driver gpu da série N para windows](/azure/virtual-machines/windows/n-series-driver-setup) ou [n-series GPU configuração para Linux](/azure/virtual-machines/linux/n-series-driver-setup) para sistemas operacionais, drivers, instalações e etapas de verificação suportadas.

## <a name="deployment-considerations"></a>Considerações de implantação

- Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

- As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). As NCv2, NCv3, ND, NDv2, e’ NVv2são compatíveis apenas com discos de VM que executam backup em SSD (Armazenamento em Disco Premium).

- Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- Talvez seja necessário aumentar a cota de núcleos (por região) na sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV, ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação de suporte ao cliente on-line](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para computação](sizes-compute.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
