---
title: Tamanhos de VM do Linux do Azure - gerações anteriores | Microsoft Docs
description: Lista as gerações anteriores de tamanhos disponíveis para máquinas virtuais Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 7f13ab3cd6ff765bc3b1bee8e8fad7e7273f6c7d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673922"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Gerações anteriores de tamanhos de máquinas virtuais

Esta seção fornece informações sobre gerações anteriores de tamanhos de máquinas virtuais. Esses tamanhos ainda podem ser usados, mas as gerações mais recentes estão disponíveis.

## <a name="f-series"></a>Série F

A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  

As VMs da série F são uma ótima opção para as cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou armazenamento temporário por vCPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

ACU: 210 - 250

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS-series <sup>1</sup>

A série Fs fornece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210 - 250

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup> O throughput máximo de disco (IOPS ou MBps) possível com uma VM série Fs pode ser limitado pelo número, tamanho e striping do disco(s) conectado.  Para obter detalhes, consulte o design para alto desempenho para [Windows](windows/premium-storage-performance.md) ou [Linux](linux/premium-storage-performance.md).  


## <a name="nvv2-series"></a>Série NVv2

**Recomendação de tamanho mais novo**: Série [NVv3](nvv3-series.md)

As máquinas virtuais da série NVv2 contam com as GPUs [Tesla M60 NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e a tecnologia NVIDIA GRID com CPUs Intel Broadwell. Essas máquinas virtuais são direcionadas para aplicativos com gráficos acelerados por GPU e áreas de trabalho virtuais em que os clientes querem visualizar dados, simular resultados para exibição, trabalhar no CAD ou renderizar e transmitir conteúdo por streaming. Além disso, essas máquinas virtuais podem executar cargas de trabalho de precisão simples, como codificação e renderização. As máquinas virtuais NVv2 são compatíveis com o Armazenamento Premium e oferecem duas vezes mais memória (RAM) em comparação com a Série NV anterior.  

Cada GPU em instâncias da NVv2 vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Gerações mais antigas de tamanhos de máquinas virtuais

Esta seção fornece informações sobre gerações mais antigas de tamanhos de máquinas virtuais. Esses tamanhos ainda são suportados, mas não receberão capacidade adicional. Existem tamanhos mais novos ou alternativos que geralmente estão disponíveis. Consulte [Sizes for Linux virtual machines in Azure](linux/sizes.md) para escolher os tamanhos de VM que melhor se adequarão à sua necessidade.  

Para obter mais informações sobre como redimensionar um VM Linux, consulte [Redimensionar um VM Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>A Básico  

**Recomendação de tamanho mais novo**: Série [Av2](av2-series.md)

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Os tamanhos da camada básicos são principalmente para as cargas de trabalho de desenvolvimento e outros aplicativos que não requerem o balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

| Tamanho – Tamanho\Nome | vCPU | Memória|NICs (Máx.)| Tamanho máximo do disco temporário | Máx. de discos de dados (1023 GB cada)| Máx. IOPS (300 por disco) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 x 300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2 x 300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4 x 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 x 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 x 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell

No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

### <a name="a-series"></a>Séria A  

**Recomendação de tamanho mais novo**: Série [Av2](av2-series.md)

ACU: 50-100

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva  

**Recomendação de tamanho mais novo**: Série [Av2](av2-series.md)

ACU: 225

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 a 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.  

> [!NOTE]
> As VMs A8 – A11 estão previstas para a aposentadoria em 3/2021. Para obter mais informações, consulte [o Guia de Migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Série D  

**Recomendação de tamanho mais novo**: Série [Dv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> Família VM pode ser executada em uma das seguintes CPU's: Intel Xeon de 2,2 GHz® E5-2660 v2, Intel Xeon de 2,4 GHz® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - otimizado para memória  

**Recomendação de tamanho mais novo**: Série [Dv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> Família VM pode ser executada em uma das seguintes CPU's: Intel Xeon de 2,2 GHz® E5-2660 v2, Intel Xeon de 2,4 GHz® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Pré-visualização: SÉRIE DC

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

A série DC usa a última geração do processador Intel XEON E-2176G de 3.7GHz com tecnologia SGX, e com a tecnologia Intel Turbo Boost pode ir até 4.7GHz. 

| Tamanho          | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> As VMs da série DC são [VMs de geração 2](./linux/generation-2.md#creating-a-generation-2-vm) e só suportam `Gen2` imagens.


### <a name="ds-series"></a>Série DS  

**Recomendação de tamanho mais novo**: Série [Dsv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> Família VM pode ser executada em uma das seguintes CPU's: Intel Xeon de 2,2 GHz® E5-2660 v2, Intel Xeon de 2,4 GHz® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - otimizado para memória  

**Recomendação de tamanho mais novo**: Série [Dsv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Máximo de armazenamento em cache e armazenamento temporário: IOPS/MBps (tamanho de cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte o design para alto desempenho para [Windows](windows/premium-storage-performance.md) ou [Linux](linux/premium-storage-performance.md).
<sup>2</sup> VM Family pode ser executado em uma das seguintes CPU's: Intel Xeon de 2,2 GHz® E5-2660 v2, Intel Xeon de 2,4 GHz® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

A série Ls oferece até 32 vCPUs, usando a [família de processadores Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls obtém o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por vCPU.

A série Ls não é compatível com a criação de um cache local para aumentar o IOPS que pode ser obtido por discos de dados duráveis. O alto throughput e IOPS do disco local torna as VMs da série Ls ideais para lojas NoSQL como Apache Cassandra e MongoDB que replicam dados em várias VMs para alcançar a persistência no caso de falha de um único VM.

ACU: 180-240

Armazenamento Premium: com suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Throughput máximo de armazenamento temporário (IOPS/MBps) | Máximo throughput de disco sem cache (IOPS/MBps) | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

A taxa de transferência máxima possível do disco com VMs da série Ls pode ser limitada pelo número, tamanho e divisão dos discos anexados. Para obter detalhes, consulte o design para alto desempenho para [Windows](windows/premium-storage-performance.md) ou [Linux](linux/premium-storage-performance.md).

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.

### <a name="gs-series"></a>Série GS

ACU: 180 - 240 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Máximo throughput de disco sem cache: IOPS/MBps | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, consulte o design para alto desempenho para [Windows](windows/premium-storage-performance.md) ou [Linux](linux/premium-storage-performance.md).

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

<sup>3</sup> Tamanhos limitados de núcleos disponíveis.

<br>

### <a name="g-series"></a>Série G

ACU: 180 - 240

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Throughput de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados máximos/throughput: IOPS | Máxima NICs/Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.
<br>

# <a name="nv-series"></a>Série NV
**Recomendação de tamanho mais**novo: [série NVv3](nvv3-series.md) e [série NVv4](nvv4-series.md)

As máquinas virtuais da série NV têm a tecnologia das GPUs [ Tesla M60 da NVIDIA ](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. As VMs da série NV também são alimentadas por CPUs Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

Armazenamento Premium: sem suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: não suportado

Atualizações de preservação de memória: não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de um cartão M60.
<br>

## <a name="other-sizes"></a>Outros tamanhos

* [Propósito geral](sizes-general.md)
* [Otimizado para computação](sizes-compute.md)
* [Otimizado para memória](sizes-memory.md)
* [Otimizado para armazenamento](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Computação de alto desempenho](sizes-hpc.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
