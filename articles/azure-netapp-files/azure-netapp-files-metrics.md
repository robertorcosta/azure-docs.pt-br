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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707774"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Azure NetApp Files fornece métricas sobre armazenamento alocado, uso de armazenamento real, IOPS de volume e latência. Analisando essas métricas, você pode obter um melhor entendimento sobre o padrão de uso e o desempenho do volume de suas contas do NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de uso de pools de capacidade

- *Tamanho alocado do pool*   
    O tamanho provisionado do pool.

- *Pool alocado para o tamanho do volume*  
    O total de cotas de volume (GiB) em um determinado pool de capacidade (ou seja, o total dos tamanhos provisionados pelos volumes no pool de capacidade).  
    Esse tamanho é o tamanho que você selecionou durante a criação do volume.  

- *Tamanho consumido do pool*  
    O total de espaço lógico (GiB) usado em volumes em um pool de capacidade.  

- *Tamanho total do instantâneo do pool*    
    A soma do tamanho do instantâneo de todos os volumes no pool.

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

- *Latência média de leitura*   
    O tempo médio para leituras do volume em milissegundos.
- *Latência média de gravação*   
    O tempo médio para gravações do volume em milissegundos.
- *IOPS de leitura*   
    O número de leituras para o volume por segundo.
- *IOPS de gravação*   
    O número de gravações no volume por segundo.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Métricas de replicação de volume

- *O status de replicação do volume é íntegro*   
    A condição da relação de replicação. 

- *A replicação de volume está sendo transtransferência*    
    Se o status da replicação do volume é ' Transferindo '. 
 
- *Tempo de retardo da replicação do volume*   
    A quantidade de tempo em segundos pela qual os dados no espelho atrasam a origem. 

- *Duração da última transferência da replicação de volume*   
    A quantidade de tempo em segundos que levou para a última transferência ser concluída. 

- *Tamanho da última transferência da replicação de volume*    
    O número total de bytes transferidos como parte da última transferência. 

- *Progresso da replicação do volume*    
    A quantidade total de dados transferidos para a operação de transferência atual. 

- *Transferência total de replicação de volume*   
    Os bytes cumulativos transferidos para a relação. 

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
