---
title: Níveis de serviço no Azure NetApp Files | Microsoft Docs
description: Descreve o desempenho de taxa de transferência para os níveis de serviço do Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832574"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviço do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pelo throughput máximo permitido para um volume no pool de capacidade com base na cota atribuída ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço suportados

O Azure NetApp Files suporta três níveis de serviço: *Ultra*, *Premium*e *Standard*. 

* <a name="Ultra"></a>Armazenamento ultra

    O nível de armazenamento Ultra fornece até 128 MiB/s de throughput por 1 TiB de cota de volume atribuída. 

* <a name="Premium"></a>Armazenamento premium

    O nível de armazenamento Premium fornece até 64 MiB/s de throughput por 1 TiB de cota de volume atribuída. 

* <a name="Standard"></a>Armazenamento padrão

    O nível de armazenamento Padrão fornece até 16 MiB/s de throughput por 1 TiB de cota de volume atribuída.

## <a name="throughput-limits"></a>Limites de taxa de transferência

O limite de throughput para um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço do pool de capacidade ao qual o volume pertence
* A cota atribuída ao volume  

Este conceito é ilustrado no diagrama abaixo:

![Ilustração do nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

No Exemplo 1 acima, um volume de um pool de capacidade com o nível de armazenamento Premium atribuído 2 TiB de cota será atribuído um limite de throughput de 128 MiB/s (2 TiB * 64 MiB/s). Este cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real de volume.

No Exemplo 2 acima, um volume de um pool de capacidade com o nível de armazenamento Premium atribuído a 100 GiB de cota será atribuído um limite de throughput de 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Este cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real de volume.

## <a name="next-steps"></a>Próximas etapas

- Confira a [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) para ver o preço de diferentes níveis de serviço
- Consulte [o modelo custo para Arquivos Azure NetApp](azure-netapp-files-cost-model.md) para o cálculo do consumo de capacidade em um pool de capacidade 
- [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- Consulte [O Contrato de Nível de Serviço (SLA) para Arquivos Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp/)