---
title: Configurar a política de exportação para um volume NFS usando Azure NetApp Files | Microsoft Docs
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS usando o Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d73ac199df03f4d789db0634be1e926afc77e623
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597540"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume do NFS

Opcionalmente, você pode configurar a política de exportação para controlar o acesso a um volume Azure NetApp Files. Azure NetApp Files política de exportação dá suporte apenas a volumes NFS.  Há suporte para NFSv3 e NFSv4. 

## <a name="steps"></a>Etapas 

1.  Clique na folha **criar política de exportação** da folha gerenciar volume. 

2.  Especifique as informações para os seguintes campos para criar uma regra de política de exportação:   
    *  @No__t_1 de **índice**  
        Especifique o número de índice para a regra.  
        Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com sua ordem na lista de números de índice. As regras com números de índice inferiores são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes permitidos**    
        Especifique o valor em um dos seguintes formatos:  
        * Endereço IPv4, por exemplo, `10.1.12.24` 
        * Endereço IPv4 com uma máscara de sub-rede expressa como um número de bits, por exemplo, `10.1.12.10/4`

    * **Acessar**  
        Selecione um dos seguintes tipos de acesso:  
        * Sem acesso 
        * Ler & gravação
        * Somente leitura

    ![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Próximos passos 
* [Gerenciar volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
