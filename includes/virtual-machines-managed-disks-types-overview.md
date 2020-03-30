---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262022"
---
Os discos gerenciados do Azure atualmente oferecem quatro tipos de disco, cada tipo é direcionado para cenários específicos do cliente.

## <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir fornece uma comparação de discos ultra, unidades de estado sólido premium (SSD), SSD padrão e discos rígidos padrão (HDD) para discos gerenciados para ajudá-lo a decidir o que usar.

|   | Disco Ultra   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho intensivas em IO, como [SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)bancos de dados de alto nível (por exemplo, SQL, Oracle) e outras cargas de trabalho pesadas de transações.   |Cargas de trabalho confidenciais produção e desempenho   |Servidores Web, aplicativos empresariais pouco usados e desenvolvimento/teste   |Backup, não crítico, acesso não frequente   |
|Tamanho máximo do disco   |65.536 GiB (GibiByte)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|Taxa de transferência máxima   |2.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS Máxima   |160.000    |20,000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Disco Ultra

Os discos Ultra do Azure proporcionam uma alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência e consistente para as VMs de IaaS do Azure. Alguns benefícios adicionais dos discos ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar suas máquinas virtuais (VM). Os discos Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas. Os discos Ultra só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um disco Ultra, você pode configurar a capacidade e o desempenho do disco de forma independente. Os discos ultra vêm em vários tamanhos fixos, variando de 4 GiB até 64 TiB, e apresentam um modelo de configuração de desempenho flexível que permite configurar iOPS e throughput de forma independente.

Alguns dos principais recursos dos ultradiscos são:

- Capacidade do disco: A capacidade dos discos ultra varia de 4 GiB até 64 TiB.
- IOPS de disco: Os discos ultra suportam limites iOPS de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para obter o IOPS que você provisionou, certifique-se de que o IOPS de disco selecionado seja menor que o limite de IOPS VM. O IOPS mínimo garantido por disco é de 2 IOPS/GiB, com um mínimo de base total de 100 IOPS. Por exemplo, se você tivesse um disco ultra 4 GiB, você terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Throughput de disco: Com os discos ultra, o limite de throughput de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (onde MBps = 10^6 Bytes por segundo). O rendimento mínimo garantido por disco é de 4KiB/s para cada IOPS provisionado, com um mínimo de base total de 1 MBps.
- Os discos ultra suportam ajustar os atributos de desempenho do disco (IOPS e throughput) em tempo de execução sem separar o disco da máquina virtual. Depois que uma operação de redimensionamento de desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração entre em vigor efetivamente. Há um limite de quatro operações de redimensionamento de desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionamento de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do Disco (GiB)  |Tampa IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2.000         |

### <a name="ga-scope-and-limitations"></a>Escopo e limitações do GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
