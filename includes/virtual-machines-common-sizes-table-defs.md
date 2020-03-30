---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279196"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de tamanhos

- A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Quando você comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3) lembre-se que os números de capacidade dados no GiB podem parecer menores. Por exemplo, 1023 GiB = 1098,4 GB.
- A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
- Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**.  Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
- Se você quiser obter o melhor desempenho para suas VMs, você deve limitar o número de discos de dados a dois discos por vCPU.
- **Largura de banda de rede esperada** é a largura de banda agregada máxima alocada por tipo de VM em todas as NICs para todos os destinos. Para obter mais informações, consulte [a largura de banda da rede](../articles/virtual-network/virtual-machine-network-throughput.md)da máquina virtual .

  Limites superiores não são garantidos. Os limites oferecem orientação para selecionar o tipo de VM certo para a aplicação pretendida. O desempenho real da rede dependerá de vários fatores, incluindo congestionamento de rede, cargas de aplicativos e configurações de rede. Para obter informações sobre como otimizar o throughput da rede, consulte [Otimizar o throughput de rede para máquinas virtuais Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para alcançar o desempenho de rede esperado no Linux ou Windows, você pode precisar selecionar uma versão específica ou otimizar sua VM. Para obter mais informações, consulte [Testes de largura de banda/throughput (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



