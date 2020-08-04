---
title: Configurar a política de exportação para o volume do NFS-Azure NetApp Files
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS usando o Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533183"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume do NFS

Você pode configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files. Azure NetApp Files política de exportação dá suporte a volumes que usam o protocolo NFS (NFSv3 e NFSv 4.1) e o protocolo dual (NFSv3 e SMB). 

Você pode criar até cinco regras de política de exportação.

## <a name="steps"></a>Etapas 

1.  Na página volumes, selecione o volume para o qual você deseja configurar a política de exportação e clique em **Exportar política**. 

    Você também pode configurar a política de exportação durante a criação do volume.

2.  Especifique informações para os campos a seguir para criar uma regra de política de exportação:   
    *  **Index**   
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

    * **Somente leitura** e **leitura/gravação**  
        Se você usar a criptografia Kerberos com o NFSv 4.1, siga as instruções em [Configurar a criptografia Kerberos do nfsv 4.1](configure-kerberos-encryption.md).  Para o impacto no desempenho do Kerberos, consulte [impacto no desempenho do Kerberos no nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance). 

        ![Opções de segurança do Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Acesso à raiz**  
        Especifique se a `root` conta pode acessar o volume.  Por padrão, o acesso à raiz é definido como **ativado**e a `root` conta tem acesso ao volume.

![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Próximas etapas 
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
