---
title: Série Lsv2-máquinas virtuais do Azure
description: Especificações para as VMs da série Lsv2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: b10433190ccc7703938bed2a75a5d816f073295e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587253"
---
# <a name="lsv2-series"></a>Série Lsv2

A série Lsv2 apresenta alta taxa de transferência, baixa latência, armazenamento NVMe no processador [AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) com um aumento core de 2,55GHz e aumento máximo de 3,0GHz. As VMs da série Lsv2 vêm em tamanhos de 8 para vCPU 80 em uma configuração de vários thread simultânea.  Há 8 GiB de memória por vCPU e um dispositivo de NVMe SSD M.2 de 1,92 TB por 8 vCPUs, com até 19,2 TB (10x1.92TB) disponível no L80s v2.

> [!NOTE]
> As VMs da série Lsv2 são otimizadas para usar o disco local no nó anexado diretamente à VM em vez de usar discos de dados duráveis. Isso permite maior IOPs / taxa de transferência para suas cargas de trabalho. A Lsv2 e a série ls não dão suporte à criação de um cache local para aumentar o IOPs atingível por discos de dados duráveis.
>
> A alta taxa de transferência e o IOPs do disco local tornam as VMs da série Lsv2 ideais para os repositórios NoSQL, como o Apache Cassandra e o MongoDB, que replicam dados em várias VMs para alcançar a persistência em caso de falha de uma única VM.
>
> Para saber mais, consulte otimizar o desempenho nas máquinas virtuais da série Lsv2 para [Windows](../virtual-machines/windows/storage-performance.md) ou [Linux](../virtual-machines/linux/storage-performance.md).  

ACU: 150-175

Armazenamento Premium: com suporte

Armazenamento em cache Premium: sem suporte

| Tamanho | vCPU | Memória (GiB) | Disco temporário<sup>1</sup> (GiB) | Discos NVMe<sup>2</sup> | Taxa de transferência de disco de NVMe<sup>3</sup> (IOPS de leitura/Mbps) | Taxa de transferência máxima de disco de dados não armazenados em cache (IOPs/MBps)<sup>4</sup> | Discos de dados máximos | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1,5 m/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6 vezes 1.92 TB  | 2.2 m/14000   | 48000/960  | 32 | 8/16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9 m/16000   | 64000/1280 | 32 | 8/16000 + |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10x1.92TB | 3,8 m/20000 | 80000/1400 | 32 | 8/16000 + |

<sup>1</sup> As VMs da série Lsv2 têm um disco de recurso temporário baseado em SCSI padrão para uso de arquivo de paginação/troca de sistema operacional (D: no Windows, /dev/sdb no Linux). Esse disco fornece 80 GiB de armazenamento, 4.000 IOPS e taxa de transferência de 80 MBps a cada 8 VCPUs (por exemplo, Standard_L80s_v2 fornece 800 GiB a 40.000 IOPS e 800 MBPS). Isso garante que as unidades de NVMe podem ser totalmente dedicadas para uso do aplicativo. Esse disco é efêmero e todos os dados serão perdidos quando ele for parado/desalocado.

<sup>2</sup> Discos locais de NVMe são efêmeros, os dados serão perdidos nesses discos se você parar/desalocar a VM.

<sup>3</sup> A tecnologia Hyper-V NVMe Direct fornece acesso limitado a unidades de NVMe locais mapeadas com segurança no espaço VM de convidado.  Alcançar o desempenho máximo requer ousar a compilação mais recente do WS2019 ou Ubuntu 18.04 ou 16.04 do Azure Marketplace.  O desempenho de gravação varia com base no tamanho de E/S, carga de unidade e a utilização da capacidade.

<sup>4</sup> VMs da série Lsv2 não fornecem o cache de host para o disco de dados uma vez que não beneficiam as cargas de trabalho Lsv2.  No entanto, VMs Lsv2 podem acomodar a opção de disco do SO efêmero da VM do Azure (até 30 GiB).

<sup>5</sup> VMs com mais de 64 vCPUs exigem um destes sistemas operacionais convidados com suporte:

- Windows Server 2016 ou posterior
- Ubuntu 16, 4 LTS ou posterior, com kernel ajustado do Azure (kernel 4,15 ou posterior)
- SLES 12 SP2 ou posterior
- RHEL ou CentOS versão 6,7 até 6,10, com o pacote LIS fornecido pela Microsoft 4.3.1 (ou posterior) instalado
- RHEL ou CentOS versão 7,3, com o pacote LIS fornecido pela Microsoft 4.2.1 (ou posterior) instalado
- RHEL ou CentOS versão 7,6 ou posterior
- Oracle Linux com UEK4 ou posterior
- Debian 9 com o kernel de backports, Debian 10 ou posterior
- CoreOS com um kernel 4,14 ou posterior

## <a name="size-table-definitions"></a>Definições da tabela de tamanhos

- A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB
- A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
- Se você quiser obter o melhor desempenho para suas VMs, será necessário limitar o número de discos de dados a 2 discos por vCPU.
- **Largura de banda de rede esperada** é a [largura de banda agregada máxima alocada por tipo de VM](../virtual-network/virtual-machine-network-throughput.md) em todas as NICs para todos os destinos. Os limites superiores não são garantidos, mas devem fornecer diretrizes para selecionar o tipo de VM correto para o aplicativo desejado. O desempenho real da rede dependerá de uma variedade de fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede. Para saber mais sobre como otimizar a taxa de transferência de rede, veja [Otimização da taxa de transferência de rede para Windows e Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md). Para obter o desempenho de rede esperado no Linux ou no Windows, poderá necessário selecionar uma versão específica ou otimizar sua VM. Para saber mais, veja [Como testar a taxa de transferência da máquina virtual de forma confiável](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.