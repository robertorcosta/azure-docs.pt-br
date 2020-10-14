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
ms.date: 10/13/2020
ms.author: b-juche
ms.openlocfilehash: 54e6f4abd5ca6d15a4cc5a7bc9015abb005296a0
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013637"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Solucionar problemas de pool de capacidade

Este artigo descreve as resoluções para os problemas que você pode ter ao gerenciar pools de capacidade, incluindo a operação de alteração de pool. 

## <a name="issues-managing-a-capacity-pool"></a>Problemas de gerenciamento de um pool de capacidade 

|     Condição de erro    |     Resolução    |
|-|-|
| Problemas ao criar um pool de capacidade |  Certifique-se de que a contagem de pool de capacidade não exceda o limite. Consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md).  Se a contagem for menor do que o limite e você ainda tiver problemas, arquivo a um tíquete de suporte e especifique o nome do pool de capacidade. |
| Problemas ao excluir um pool de capacidade  |  Certifique-se de remover todos os volumes de Azure NetApp Files e instantâneos na assinatura em que você está tentando excluir o pool de capacidade. <br> Se você já tiver removido todos os volumes e instantâneos e ainda não puder excluir o pool de capacidade, as referências aos recursos ainda poderão existir sem serem exibidas no Portal. Nesse caso, arquivo um tíquete de suporte e especifique que você executou as etapas recomendadas acima. |
| Falha na criação ou modificação do volume com `Requested throughput not available` erro | A taxa de transferência disponível para um volume é determinada pelo tamanho do pool de capacidade e o nível de serviço. Se você não tiver uma taxa de transferência suficiente, deverá aumentar o tamanho do pool ou ajustar a taxa de transferência do volume existente. | 

## <a name="issues-moving-a-capacity-pool"></a>Problemas ao mover um pool de capacidade 
|     Condição de erro    |     Resolução    |
|-|-|
| Não é permitido alterar o pool de capacidade para um volume. | Talvez você ainda não esteja autorizado a usar esse recurso. <br> O recurso para mover um volume para outro pool de capacidade está atualmente em versão prévia. Se você estiver usando esse recurso pela primeira vez, precisará registrar o recurso primeiro e definir `-FeatureName ANFTierChange` . Consulte as etapas de registro em [alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md). |
| O tamanho do pool de capacidade é muito pequeno para o tamanho total do volume. |  O erro é um resultado do pool de capacidade de destino não ter a capacidade disponível para o volume que está sendo movido.  <br> Aumente o tamanho do pool de destino ou escolha outro pool que seja maior.  Consulte [redimensionar um pool de capacidade ou um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
| Não é possível mover um volume quando o pool de capacidade de destino tem um tipo de criptografia diferente do pool de capacidade original.  Por exemplo, você está mudando de criptografia dupla para criptografia única, ou vice-versa.  | Selecione um pool de capacidade de destino com o mesmo tipo de criptografia que o pool de capacidade de origem.   |
|  A alteração do pool não pode ser concluída porque um volume chamado `'{source pool name}'` já existe no pool de destino `'{target pool name}'` | Esse erro ocorre porque o volume com o mesmo nome já existe no pool de capacidade de destino.  Selecione outro pool de capacidade que não tenha um volume com o mesmo nome.   | 

## <a name="next-steps"></a>Próximas etapas  

* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerenciar um pool de capacidade de QoS manual](manage-manual-qos-capacity-pool.md)
* [Alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md)
* [Redimensionar um volume ou um pool de capacidade](azure-netapp-files-resize-capacity-pools-or-volumes.md)
