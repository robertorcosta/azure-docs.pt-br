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
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651047"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Solucionar problemas de pool de capacidade

Este artigo descreve as resoluções para os problemas que você pode ter ao gerenciar pools de capacidade. 

## <a name="error-conditions-and-resolutions"></a>Condições de erro e resoluções 

|     Condição de erro    |     Resolução    |
|-|-|
| Problemas ao criar um pool de capacidade |  Certifique-se de que a contagem de pool de capacidade não exceda o limite. Consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md).  Se a contagem for menor do que o limite e você ainda tiver problemas, arquivo a um tíquete de suporte e especifique o nome do pool de capacidade. |
| Problemas ao excluir um pool de capacidade  |  Certifique-se de remover todos os volumes de Azure NetApp Files e instantâneos na assinatura em que você está tentando excluir o pool de capacidade. <br> Se você já tiver removido todos os volumes e instantâneos e ainda não puder excluir o pool de capacidade, as referências aos recursos ainda poderão existir sem serem exibidas no Portal. Nesse caso, arquivo um tíquete de suporte e especifique que você executou as etapas recomendadas acima. |
| Falha na criação ou modificação do volume com `Requested throughput not available` erro | A taxa de transferência disponível para um volume é determinada pelo tamanho do pool de capacidade e o nível de serviço. Se você não tiver uma taxa de transferência suficiente, deverá aumentar o tamanho do pool ou ajustar a taxa de transferência do volume existente. | 

## <a name="next-steps"></a>Próximas etapas  

* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerenciar um pool de capacidade de QoS manual](manage-manual-qos-capacity-pool.md)
