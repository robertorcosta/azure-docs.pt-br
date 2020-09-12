---
title: NCas T4 v3-Series
description: Especificações para as VMs da série NCas T4 v3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: af9f7eb21d533bc5fb365e7cbf1fb8fc18184fa7
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375220"
---
# <a name="ncast4_v3-series-in-preview"></a>Série NCasT4_v3 (em versão prévia) 

As máquinas virtuais da série NCasT4_v3 são alimentadas por GPUs [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPUs AMD EPYC 7V12 (Roma). O recurso de VMs tem até 4 GPUs NVIDIA T4 com 16 GB de memória cada, até 64 núcleos de processador EPYC de 7V12 (Roma) não multithread e 440 GiB de memória do sistema. Essas máquinas virtuais são ideais para executar cargas de trabalho ML e ia utilizando CUDA, TensorFlow, Pytorch, Caffe e outras estruturas ou as cargas de trabalho de gráficos usando a tecnologia de grade NVIDIA. A série NCasT4_v3 é ideal para a execução de cargas de trabalho de inferência.

Você pode [Enviar uma solicitação](https://aka.ms/NCT4v3Preview) para fazer parte do programa de visualização.

<br>

ACU: 230-260

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série NCasT4_v3 do Azure que executam Windows ou Linux, os drivers NVIDIA GPU devem ser instalados.

Para instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Windows](./windows/n-series-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
