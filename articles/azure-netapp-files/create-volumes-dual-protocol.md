---
title: Criar um volume de protocolo duplo (NFSv3 e SMB) para Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume que usa o protocolo duplo de NFSv3 e SMB com suporte para mapeamento de usuário LDAP.
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
ms.date: 8/11/2020
ms.author: b-juche
ms.openlocfilehash: dcdb3e8ce545227bc11cc60e3885c1a985ed34f4
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459990"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Criar um volume de protocolo duplo (NFSv3 e SMB) para Azure NetApp Files

O Azure NetApp Files dá suporte à criação de volumes usando NFS (NFSv3 e NFSv 4.1), SMBv3 ou protocolo duplo. Este artigo mostra como criar um volume que usa o protocolo duplo de NFSv3 e SMB com suporte para mapeamento de usuário LDAP.  


## <a name="before-you-begin"></a>Antes de começar 

* Você deve já configurou um pool de capacidade.  
    Consulte [configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).   
* Uma sub-rede deve ser delegada ao Azure NetApp Files.  
    Consulte [delegar uma sub-rede a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerações

* Verifique se você atende aos [requisitos de conexões de Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Crie uma zona de pesquisa inversa no servidor DNS e adicione um registro de ponteiro (PTR) do computador host do AD nessa zona de pesquisa inversa. Caso contrário, a criação do volume de protocolo duplo falhará.
* Verifique se o cliente NFS está atualizado e executando as atualizações mais recentes para o sistema operacional.

## <a name="create-a-dual-protocol-volume"></a>Criar um volume de protocolo duplo

1.  Clique na folha **Volumes** da folha Pools de Capacidade. Clique em **+ Adicionar volume** para criar um volume. 

    ![Navegar até Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Na janela criar um volume, clique em **criar**e forneça informações para os seguintes campos na guia noções básicas:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        O nome do volume precisa ser exclusivo em cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.   

        Você não pode usar `default` como o nome do volume.

    * **Pool de capacidade**  
        Especifique o pool de capacidade no qual você deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a VNet (rede virtual) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Também é possível acessar o volume na rede local por meio do ExpressRoute.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada vnet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se você quiser aplicar uma política de instantâneo existente ao volume, clique em **Mostrar seção avançada** para expandi-la, especifique se deseja ocultar o caminho do instantâneo e selecione uma política de instantâneo no menu suspenso. 

        Para obter informações sobre como criar uma política de instantâneo, consulte [gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Clique em **Protocolo** e, em seguida, conclua as seguintes ações:  
    * Selecione **protocolo duplo (NFSv3 e SMB)** como o tipo de protocolo para o volume.   

    * Selecione a conexão **Active Directory** na lista suspensa.  
    O Active Directory usado deve ter um certificado de autoridade de certificação raiz do servidor. 

    * Especifique o **caminho do volume** para o volume.   
    Esse caminho de volume é o nome do volume compartilhado. O nome deve começar com um caractere alfabético e deve ser exclusivo em cada assinatura e em cada região.  

    * Especifique o **estilo de segurança** a ser usado: NTFS (padrão) ou UNIX.

    * Opcionalmente, [Configure a política de exportação para o volume](azure-netapp-files-configure-export-policy.md).

    ![Especificar protocolo duplo](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Clique em **Examinar + Criar** para examinar os detalhes do volume. Em seguida, clique em **criar** para criar o volume.

    O volume que você criou aparece na página Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Carregar Active Directory certificado raiz público da autoridade de certificação  

1.  Siga [instalar a autoridade de certificação](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) para instalar e configurar o adiciona a autoridade de certificado. 

2.  Siga [exibir certificados com o snap-in do MMC](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) para usar o snap-in do MMC e a ferramenta Gerenciador de certificados.  
    Use o snap-in Gerenciador de certificados para localizar a raiz ou o certificado emissora para o dispositivo local. Você deve executar os comandos do snap-in de gerenciamento de certificados de uma das seguintes configurações:  
    * Um cliente baseado em Windows que ingressou no domínio e tenha o certificado raiz instalado 
    * Outro computador no domínio que contém o certificado raiz  

3. Exporte o certificado raiz.  
    Verifique se o certificado foi exportado no X. 509 codificado em base-64 (. CER) formato: 

    ![Assistente de Exportação de Certificados](../media/azure-netapp-files/certificate-export-wizard.png)

4. Vá para a conta do NetApp do volume de protocolo duplo, clique em **Active Directory conexões**e carregue o certificado de autoridade de certificação raiz usando a janela **ingressar Active Directory** :  

    ![Certificado de autoridade de certificação raiz do servidor](../media/azure-netapp-files/server-root-ca-certificate.png)

    Verifique se o nome da autoridade de certificação pode ser resolvido pelo DNS. Esse nome é o campo "emitido por" ou "emissor" no certificado:  

    ![Informações de certificado](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Gerenciar atributos POSIX LDAP

Você pode gerenciar atributos POSIX, como UID, diretório base e outros valores usando o snap-in Active Directory usuários e computadores do MMC.  O exemplo a seguir mostra o editor de atributo Active Directory:  

![Editor de atributos Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>Configurar o cliente NFS 

Siga as instruções em [configurar um cliente NFS para Azure NetApp files](configure-nfs-clients.md) para configurar o cliente NFS.  

## <a name="next-steps"></a>Próximas etapas  

* [Perguntas frequentes sobre protocolo duplo](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md) 
