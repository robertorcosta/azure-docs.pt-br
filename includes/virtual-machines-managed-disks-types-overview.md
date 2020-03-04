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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262022"
---
Atualmente, o Azure Managed disks oferece quatro tipos de disco, cada tipo destina-se a cenários de clientes específicos.

## <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir fornece uma comparação de ultra discos, unidades de estado sólido Premium (SSD), SSD padrão e unidades de disco rígido padrão (HDD) para discos gerenciados para ajudá-lo a decidir o que usar.

|   | Ultra Disk   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho com uso intensivo de e/s, como [SAP Hana](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), bancos de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho de transações pesadas.   |Cargas de trabalho confidenciais produção e desempenho   |Servidores Web, aplicativos empresariais pouco usados e desenvolvimento/teste   |Backup, não crítico, acesso não frequente   |
|Tamanho máximo do disco   |65.536 Gibibyte (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|Taxa de transferência máxima   |2\.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS Máxima   |160.000    |20,000   |6\.000   |2\.000   |

## <a name="ultra-disk"></a>Ultra Disk

Os ultra discos do Azure fornecem alta taxa de transferência, IOPS alta e armazenamento de disco consistente de baixa latência para VMs IaaS do Azure. Alguns benefícios adicionais de ultra discos incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar suas máquinas virtuais (VM). Ultra discos são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho com transações pesadas. Ultra discos só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um disco Ultra, você pode configurar a capacidade e o desempenho do disco de forma independente. Ultra discos vêm em vários tamanhos fixos, variando de 4 GiB até 64 TiB e apresentam um modelo de configuração de desempenho flexível que permite configurar de forma independente a IOPS e a taxa de transferência.

Alguns dos principais recursos dos ultra discos são:

- Capacidade do disco: ultra disks Capacity varia de 4 GiB até 64 TiB.
- IOPS de disco: ultra disks dá suporte a limites de IOPS de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para obter o IOPS que você provisionou, verifique se os IOPS de disco selecionados são menores do que o limite de IOPS de VM. O mínimo de IOPS garantido por disco é 2 IOPS/GiB, com uma linha de base mínima de 100 IOPS. Por exemplo, se você tivesse um ultra GiB de 4 discos, terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Taxa de transferência do disco: com ultra disks, o limite de taxa de transferência de um único disco é 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 bytes por segundo). A taxa de transferência mínima garantida por disco é 4KiB/s para cada IOPS provisionado, com uma linha de base total mínima de 1 MBps.
- Ultra discos dão suporte ao ajuste dos atributos de desempenho de disco (IOPS e taxa de transferência) em tempo de execução sem desanexar o disco da máquina virtual. Depois que uma operação de redimensionamento de desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração entre em vigor efetivamente. Há um limite de quatro operações de redimensionamento de desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionamento de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do Disco (GiB)  |Limite de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1\.200         |300         |
|8     |2\.400         |600         |
|16     |4\.800         |1\.200         |
|32     |9\.600         |2\.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2\.000         |
|256     |76.800         |2\.000         |
|512     |80.000         |2\.000         |
|1\.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2\.000         |

### <a name="ga-scope-and-limitations"></a>Limitações e escopo de GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
