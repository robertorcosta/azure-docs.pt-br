---
title: Criar um volume NFS para arquivos do Azure NetApp | Microsoft Docs
description: Descreve como criar um volume NFS para arquivos do Azure NetApp.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274080"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Criar um volume NFS para o Azure NetApp Files

O Azure NetApp Files suporta volumes NFS (NFSv3 e NFSv4.1) e SMBv3. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume NFS. Se você quiser criar um volume de SMB, consulte [Criar um volume De SMB para Arquivos Azure NetApp](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configure um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Considerações 

* Decidir qual versão nfs usar  
  O NFSv3 pode lidar com uma grande variedade de casos de uso e é comumente implantado na maioria dos aplicativos corporativos. Você deve validar qual versão (NFSv3 ou NFSv4.1) seu aplicativo requer e criar seu volume usando a versão apropriada. Por exemplo, se você usar [o Apache ActiveMQ,](https://activemq.apache.org/shared-file-system-master-slave)o bloqueio de arquivos com NFSv4.1 é recomendado sobre o NFSv3. 

* Segurança  
  O suporte para bits do modo UNIX (ler, gravar e executar) está disponível para NFSv3 e NFSv4.1. O acesso ao nível raiz é necessário no cliente NFS para montar volumes NFS.

* Suporte local de usuário/grupo e LDAP para NFSv4.1  
  Atualmente, o NFSv4.1 suporta acesso raiz apenas a volumes. Consulte [Configurar o domínio padrão NFSv4.1 para arquivos do Azure NetApp](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Melhor prática

* Você deve garantir que você está usando as instruções de montagem adequadas para o volume.  Consulte [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* O cliente NFS deve estar no mesmo VNet ou VNet peered como o volume de Arquivos Do Azure NetApp. A conexão de fora do VNet é suportada; no entanto, introduzirá latência adicional e diminuirá o desempenho geral.

* Você deve garantir que o cliente NFS esteja atualizado e executando as últimas atualizações para o sistema operacional.

## <a name="create-an-nfs-volume"></a>Criar um volume NFS

1.  Clique na **lâmina Volumes** da lâmina Capacity Pools. 

    ![Navegue até Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Clique em **+ Adicionar volume** para criar um volume.  
    A janela Criar um volume é exibida.

3.  Na janela Criar um volume, clique em **Criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        Um nome de volume deve ser único dentro de cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.   

        Você não `default` pode usar como nome de volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade onde deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Você também pode acessar o volume da sua rede local através do Express Route.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada Vnet, apenas uma sub-rede pode ser delegada aos Arquivos do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **Protocolo** e, em seguida, conclua as seguintes ações:  
    * Selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique o **caminho do arquivo** que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.

        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres. 

        O caminho do arquivo deve ser único dentro de cada assinatura e de cada região. 

    * Selecione a versão do NFS (**NFSv3** ou **NFSv4.1**) para o volume.  
    * Opcionalmente, [configure a política de exportação para o volume NFS](azure-netapp-files-configure-export-policy.md).

    ![Especificar o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Clique **em Revisar + Criar** para revisar os detalhes do volume.  Em seguida, clique **em Criar** para criar o volume NFS.

    O volume criado é exibido na página Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.


## <a name="next-steps"></a>Próximas etapas  

* [Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume do NFS](azure-netapp-files-configure-export-policy.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Conheça a integração de rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
