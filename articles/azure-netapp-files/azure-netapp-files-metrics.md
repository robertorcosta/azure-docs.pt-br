---
title: Métricas do Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files fornece métricas sobre armazenamento alocado, uso de armazenamento real, IOPS de volume e latência. Use essas métricas para entender o uso e o desempenho.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 7e6ab90010d4379c1640f73a8deeba874e601daf
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513801"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Azure NetApp Files fornece métricas sobre armazenamento alocado, uso de armazenamento real, IOPS de volume e latência. Analisando essas métricas, você pode obter um melhor entendimento sobre o padrão de uso e o desempenho do volume de suas contas do NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de uso de pools de capacidade

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool alocado para o tamanho do volume*  
    O total de cotas de volume (GiB) em um determinado pool de capacidade (ou seja, o total dos tamanhos provisionados pelos volumes no pool de capacidade).  
    Esse tamanho é o tamanho que você selecionou durante a criação do volume.  
- *Tamanho consumido do pool*  
    O total de espaço lógico (GiB) usado em volumes em um pool de capacidade.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de uso de volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Tamanho consumido por volume*   
    O espaço lógico total usado em um volume (GiB).  
    Esse tamanho inclui espaço lógico usado por sistemas de arquivos ativos e instantâneos.  
- *Tamanho do instantâneo de volume*   
   O espaço lógico incremental usado por instantâneos em um volume.  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *AverageReadLatency*   
    O tempo médio para leituras do volume em milissegundos.
- *AverageWriteLatency*   
    O tempo médio para gravações do volume em milissegundos.
- *ReadIops*   
    O número de leituras para o volume por segundo.
- *WriteIops*   
    O número de gravações no volume por segundo.

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
