---
title: Tamanhos de VM do Azure-GPU | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
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
ms.openlocfilehash: d88c76afb3426dce8c68ae4bcda366fe17700aa7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566039"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas

Os tamanhos de VM otimizados para GPU são máquinas virtuais especializadas disponíveis com um ou vários GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho de visualização e com muita computação e muitos gráficos. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento.

- Os tamanhos da série [NC](nc-series.md), [NCv2](ncv2-series.md)e [NCv3](ncv3-series.md) são otimizados para aplicativos e algoritmos com uso intensivo de computação e rede. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, ia e aprendizado profundo. A série NCv3 concentra-se em cargas de trabalho de computação de alto desempenho com a tecnologia das GPUs Tesla V100 da NVIDIA. A série NC usa o processador Intel Xeon E5-2690 v3 2,60 GHz (Haswell) e as VMs da série NCv2 e NCv3 usam o processador Intel Xeon E5-2690 V4 (Broadwell).

- Os tamanhos da série [ND](nd-series.md)e da [série NDv2](ndv2-series.md) se concentram em cenários de treinamento e inferência para aprendizado profundo. Eles usam a GPU NVIDIA Tesla P40 e o processador Intel Xeon E5-2690 V4 (Broadwell). A série NDv2 usa o processador Intel Xeon Platinum 8168 (Skylake).

- Os tamanhos da série [NV](nv-series.md) e da [série NVv3](nvv3-series.md) são otimizados e desenvolvidos para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX. Essas VMs são apoiadas pela GPU Tesla M60 da NVIDIA.

- [Série NVv4](nvv4-series.md) Tamanhos de VM otimizados e projetados para VDI e visualização remota. Com GPUs particionadas, o NVv4 oferece o tamanho certo para cargas de trabalho que exigem recursos menores de GPU. Essas VMs são apoiadas pela GPU AMD Radeon instinto MI25. Atualmente, as VMs NVv4 dão suporte apenas ao sistema operacional convidado do Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA GPU devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](/azure/virtual-machines/extensions/overview).

Se você optar por instalar manualmente os drivers NVIDIA GPU, consulte [configuração do driver GPU da série n para Windows](/azure/virtual-machines/windows/n-series-driver-setup) ou [instalação do driver de GPU da série n para Linux](/azure/virtual-machines/linux/n-series-driver-setup) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="deployment-considerations"></a>Considerações de implantação

- Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

- As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). As NCv2, NCv3, ND, NDv2, e’ NVv2são compatíveis apenas com discos de VM que executam backup em SSD (Armazenamento em Disco Premium).

- Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- Talvez seja necessário aumentar a cota de núcleos (por região) na sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV, ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](/../azure-supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
