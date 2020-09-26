---
title: Solucionar problemas de pool de capacidade para Azure NetApp Files | Microsoft Docs
description: Descreve os possíveis problemas que você pode ter ao gerenciar pools de capacidade e fornece soluções para os problemas.
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
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91340867"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Solucionar problemas de pool de capacidade

Este artigo descreve as resoluções para os problemas que você pode ter ao gerenciar pools de capacidade. 

## <a name="issues-creating-a-capacity-pool"></a>Problemas ao criar um pool de capacidade

Certifique-se de que a contagem de pool de capacidade não exceda o limite. Consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md).  Se a contagem for menor do que o limite e você ainda tiver problemas, arquivo a um tíquete de suporte e especifique o nome do pool de capacidade.

## <a name="issues-deleting-a-capacity-pool"></a>Problemas ao excluir um pool de capacidade

Certifique-se de ter removido todos os volumes Azure NetApp Files e instantâneos na assinatura em que você está tentando excluir o pool de capacidade.   

Se você já tiver removido todos os volumes e instantâneos e ainda não puder excluir o pool de capacidade, as referências aos recursos ainda poderão existir sem serem exibidas no Portal. Nesse caso, arquivo um tíquete de suporte e especifique que você executou as etapas recomendadas acima. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>A criação ou modificação de volume falha com o erro "taxa de transferência não disponível"

A taxa de transferência disponível para um volume é determinada pelo tamanho do pool de capacidade e o nível de serviço. Se você não tiver uma taxa de transferência suficiente, deverá aumentar o tamanho do pool ou ajustar a taxa de transferência do volume existente.


## <a name="next-steps"></a>Próximas etapas  

* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerenciar um pool de capacidade de QoS manual](manage-manual-qos-capacity-pool.md)
