---
title: Desempenho do tamanho da VM da série HC
description: Saiba mais sobre os resultados de testes de desempenho para tamanhos de VM da série HC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603411"
---
# <a name="hc-series-virtual-machine-sizes"></a>Tamanhos de máquina virtual da série HC

Vários testes de desempenho foram executados em tamanhos da série HC. A seguir estão alguns dos resultados deste teste de desempenho.

| Carga de trabalho                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Tríade de fluxo                                    | 190 GB/s (Intel MLC AVX-512)  |
| Linpack de alto desempenho (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (RMAX) |
| Largura de banda de & de latência RDMA                        | 1, 5 microssegundos, 96,8 GB/s   |
| FIO no SSD de NVMe local                           | 1,3 GB/s de leituras, 900 MB/s gravações |  
| IOR em 4 Azure SSD Premium (p30 Managed Disks, RAID0) * *  | 780 MB/s leituras, 780 MB/gravações |

## <a name="mpi-latency"></a>Latência MPI

O teste de latência MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Latência MPI na HC do Azure.":::

## <a name="mpi-bandwidth"></a>Largura de banda MPI

O teste de largura de banda MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Largura de banda MPI na HC do Azure.":::


## <a name="mellanox-perftest"></a>Adaptador Mellanox Perftest

O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes de InfiniBand, como latência (ib_send_lat) e largura de banda (ib_send_bw). Um exemplo de comando é mostrado abaixo.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes e alguns exemplos e resultados de HPC (computação de alto desempenho) nos [Blogs da comunidade de computação técnica do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição arquitetônica de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).
