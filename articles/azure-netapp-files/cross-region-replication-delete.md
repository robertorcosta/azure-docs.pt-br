---
title: Excluir replicações para Azure NetApp Files replicação entre regiões | Microsoft Docs
description: Descreve como excluir uma conexão de replicação que não é mais necessária entre os volumes de origem e de destino.
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
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708524"
---
# <a name="delete-replications"></a>Excluir replicações

Você pode encerrar a conexão de replicação entre os volumes de origem e de destino excluindo a replicação de volume. Você pode executar a operação de exclusão do volume de origem ou de destino. A operação de exclusão remove apenas a autorização para replicação; Ele não remove a origem ou o volume de destino. 

## <a name="steps"></a>Etapas

1. Para excluir a replicação de volume, selecione **replicação** no volume de origem ou de destino.  

2. Clique em **Excluir**.    

3. Confirme a exclusão digitando **Sim** e clicando em **excluir**.   

    ![Excluir replicação](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Próximas etapas  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Exibir status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)

