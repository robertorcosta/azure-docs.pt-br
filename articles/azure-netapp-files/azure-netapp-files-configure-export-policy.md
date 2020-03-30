---
title: Configurar a política de exportação para o volume NFS - Arquivos Azure NetApp
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS usando arquivos do Azure NetApp
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551551"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume do NFS

Opcionalmente, você pode configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files. A política de exportação do Azure NetApp Files suporta apenas volumes NFS.  Tanto o NFSv3 quanto o NFSv4 são suportados. 

## <a name="steps"></a>Etapas 

1.  Clique **em Exportar política** do painel de navegação Azure NetApp Files. 

2.  Especifique informações para os campos a seguir para criar uma regra de política de exportação:   
    *  **Índice**   
        Especifique o número de índice para a regra.  
        Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com sua ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes permitidos**   
        Especifique o valor em um dos seguintes formatos:  
        * Endereço IPv4, por exemplo, `10.1.12.24` 
        * Endereço IPv4 com uma máscara de sub-rede expressada como um número de bits, por exemplo, `10.1.12.10/4`

    * **Acesso**  
        Selecione um dos seguintes tipos de acesso:  
        * Sem Acesso 
        * Leitura e Gravação
        * Somente leitura

    ![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Próximas etapas 
* [Gerenciar volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
