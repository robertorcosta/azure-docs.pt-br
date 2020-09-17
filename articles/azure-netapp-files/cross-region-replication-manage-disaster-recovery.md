---
title: Gerenciar a recuperação de desastre usando Azure NetApp Files replicação entre regiões | Microsoft Docs
description: Descreve como gerenciar a recuperação de desastre usando Azure NetApp Files replicação entre regiões.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708518"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gerenciar a recuperação de desastre usando a replicação entre regiões 

Uma replicação em andamento entre os volumes de origem e de destino (consulte [criar emparelhamento de replicação](cross-region-replication-create-peering.md)) prepara você para um evento de recuperação de desastre. 

Quando esse evento ocorre, você pode [fazer failover para o volume de destino](#break-replication-peering-to-activate-the-destination-volume), permitindo que o cliente leia e grave no volume de destino. 

Após a recuperação de desastre, você pode fazer failback para o volume de origem com uma [operação de ressincronização](#resync-replication-to-reactivate-the-source-volume) que substitui os dados do volume de origem pelos dados do volume de destino.  Em seguida, [restabeleça a replicação de origem para destino](#reestablish-source-to-destination-replication) e remonte o volume de origem para o cliente acessar. 

Os detalhes são descritos abaixo. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Interromper o emparelhamento de replicação para ativar o volume de destino

Quando você precisar ativar o volume de destino (por exemplo, quando desejar fazer failover para a região de destino), será necessário interromper o emparelhamento de replicação e montar o volume de destino.  

1. Para interromper o emparelhamento de replicação, selecione o volume de destino. Clique em **replicação** em serviço de armazenamento.  

2.  Verifique os campos a seguir antes de continuar:  
    * Verifique se o estado do espelho mostra ***espelhado***.   
        Não tente interromper o emparelhamento de replicação se o estado do espelho mostrar não *inicializado*.
    * Verifique se o status da relação mostra ***ocioso***.   
        Não tente interromper o emparelhamento de replicação se o status da relação mostrar *transferência*.   

    Consulte [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md). 

3.  Clique em **interromper emparelhamento**.  

4.  Digite **Sim** quando solicitado e clique no botão de **interrupção** . 

    ![Interromper emparelhamento de replicação](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Monte o volume de destino seguindo as etapas em [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Esta etapa permite que um cliente acesse o volume de destino.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Ressincronizar a replicação para reativar o volume de origem   

Após a recuperação de desastre, você pode reativar o volume de origem executando uma operação de ressincronização.  A operação de ressincronização reverte o processo de replicação e sincroniza os dados do volume de destino para o volume de origem.  

> [!IMPORTANT] 
> A operação de ressincronização substitui os dados do volume de origem pelos dados do volume de destino.  A interface do usuário avisa sobre o potencial de perda de dados. Você será solicitado a confirmar a ação de ressincronização antes que a operação seja iniciada.

1. Para ressincronizar a replicação, selecione o volume de *origem* . Clique em **replicação** em serviço de armazenamento. Em seguida, clique em **ressincronizar**.  

2. Digite **Sim** quando solicitado e clique no botão **ressincronizar** . 
 
    ![Ressincronizar replicação](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Monitore o status de integridade do volume de origem seguindo as etapas em [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md).   
    Quando o status de integridade do volume de origem mostra os valores a seguir, a operação de ressincronização é concluída e as alterações feitas no volume de destino agora são capturadas no volume de origem:   

    * Estado espelhado: *espelhado*  
    * Estado de transferência: *ocioso*  

## <a name="reestablish-source-to-destination-replication"></a>Restabelecer a replicação de origem para destino

Após a conclusão da operação de ressincronização do destino para a origem, você precisará interromper o emparelhamento de replicação novamente para restabelecer a replicação de origem para destino. Você também deve remontar o volume de origem para que o cliente possa acessá-lo.  

1. Interromper o emparelhamento de replicação:  
    a. Selecione o volume de *destino* . Clique em **replicação** em serviço de armazenamento.  
    b. Verifique os campos a seguir antes de continuar:   
    * Verifique se o estado do espelho mostra ***espelhado***.   
    Não tente interromper o emparelhamento de replicação se o estado do espelho mostrar não *inicializado*.  
    * Verifique se o status da relação mostra ***ocioso***.   
    Não tente interromper o emparelhamento de replicação se o status da relação mostrar *transferência*.    

        Consulte [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md). 

    c. Clique em **interromper emparelhamento**.   
    d. Digite **Sim** quando solicitado e clique no botão de **interrupção** .  

2. Ressincronize o volume de origem com o volume de destino:  
    a. Selecione o volume de *destino* . Clique em **replicação** em serviço de armazenamento. Em seguida, clique em **ressincronizar**.   
    b. Digite **Sim** quando solicitado e clique no botão **ressincronizar** .

3. Remonte o volume de origem seguindo as etapas em [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Esta etapa permite que um cliente acesse o volume de origem.

## <a name="next-steps"></a>Próximas etapas  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Solução de problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)

