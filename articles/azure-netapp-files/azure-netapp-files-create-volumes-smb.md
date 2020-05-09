---
title: Criar um volume SMB para Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume SMB para Azure NetApp Files.
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
ms.date: 04/30/2020
ms.author: b-juche
ms.openlocfilehash: 7dfc17825fab6c9a5f0d832318cb1d57271c56da
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625505"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files dá suporte a volumes NFS e SMBv3. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume SMBv3. Se você quiser criar um volume de NFS, consulte [criar um volume de NFS para Azure NetApp files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para conexões de Active Directory

 Você precisa criar conexões Active Directory antes de criar um volume SMB. Os requisitos para conexões de Active Directory são os seguintes: 

* A conta de administrador que você usa deve ter a capacidade de criar contas de computador no caminho da UO (unidade organizacional) que você especificará.  

* As portas adequadas devem estar abertas no servidor Windows Active Directory (AD) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web do AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta de eco    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* A topologia do site para o Active Directory Domain Services de destino deve aderir às práticas recomendadas, em particular a VNet do Azure onde Azure NetApp Files é implantado.  

    O espaço de endereço para a rede virtual em que Azure NetApp Files está implantado deve ser adicionado a um site de Active Directory novo ou existente (no qual um controlador de domínio acessível pelo Azure NetApp Files é). 

* Os servidores DNS especificados devem estar acessíveis a partir da [sub-rede delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) do Azure NetApp files.  

    Consulte as [diretrizes para Azure NetApp files planejamento de rede](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topologias de rede com suporte.

    Os NSGs (grupos de segurança de rede) e os firewalls devem ter regras configuradas adequadamente para permitir solicitações de tráfego de Active Directory e DNS. 

* O Azure NetApp Files sub-rede delegada deve ser capaz de alcançar todos os controladores de domínio de Active Directory Domain Services (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, pode ocorrer interrupção do serviço.  

    Se você tiver controladores de domínio inacessíveis pelo Azure NetApp Files sub-rede delegada, poderá especificar um site de Active Directory durante a criação da conexão Active Directory.  Azure NetApp Files precisa se comunicar somente com controladores de domínio no site em que o espaço de endereço de sub-rede delegado Azure NetApp Files é.

    Consulte [projetando a topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre serviços e sites do AD. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Consulte Azure NetApp Files [FAQs do SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais do AD. 

## <a name="decide-which-domain-services-to-use"></a>Decidir quais serviços de domínio usar 

O Azure NetApp Files dá suporte a [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) e Azure Active Directory Domain Services (AADDS) para conexões do AD.  Antes de criar uma conexão do AD, você precisa decidir se deseja usar o ADDS ou AADDS.  

Para obter mais informações, consulte [comparar Active Directory Domain Services autogerenciados, Azure Active Directory e Azure Active Directory Domain Services gerenciados](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Você pode usar o escopo de [serviços e sites de Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) preferencial para Azure NetApp files. Essa opção permite leituras e gravações em Active Directory Domain Services (ADDS) controladores de domínio que são [acessíveis pelo Azure NetApp files](azure-netapp-files-network-topologies.md). Ele também impede que o serviço se comunique com controladores de domínio que não estão no site Active Directory sites e serviços especificados. 

Para localizar o nome do seu site ao usar o ADDS, você pode entrar em contato com o grupo administrativo em sua organização responsável pelo Active Directory Domain Services. O exemplo a seguir mostra o plug-in Active Directory sites e serviços em que o nome do site é exibido: 

![Serviços e Sites do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Ao configurar uma conexão do AD para Azure NetApp Files, especifique o nome do site no escopo do campo **nome do site do AD** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para a configuração do Azure Active Directory Domain Services (AADDS) e diretrizes, consulte a [documentação do Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

Considerações adicionais sobre o AADDS se aplicam para Azure NetApp Files: 

* Verifique se a VNet ou a sub-rede em que AADDS está implantado está na mesma região do Azure que a implantação de Azure NetApp Files.
* Se você usar outra VNet na região em que Azure NetApp Files é implantado, deverá criar um emparelhamento entre os dois VNets.
* Azure NetApp Files dá `user` suporte `resource forest` a tipos e.
* Para tipo de sincronização, você pode `All` selecionar `Scoped`ou.   
    Se você selecionar `Scoped`, verifique se o grupo correto do Azure ad está selecionado para acessar compartilhamentos SMB.  Se você não tiver certeza, poderá usar o `All` tipo de sincronização.
* O uso da SKU Enterprise ou Premium é necessário. Não há suporte para o SKU Standard.

Ao criar uma conexão Active Directory, observe as seguintes especificações para AADDS:

* Você pode encontrar informações para **DNS primário**, **DNS secundário**e **nome de domínio DNS do AD** no menu AADDS.  
Para servidores DNS, dois endereços IP serão usados para configurar a conexão de Active Directory. 
* O **caminho da unidade organizacional** é `OU=AADDC Computers`.  
Essa configuração é definida no **Active Directory conexões** na **conta do NetApp**:

  ![Caminho da unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* As credenciais do **nome** de usuário podem ser qualquer usuário que seja membro do grupo do Azure ad **Administradores do DC do Azure ad**.


## <a name="create-an-active-directory-connection"></a>Criar uma conexão Active Directory

1. Em sua conta do NetApp, clique em **Active Directory conexões**e clique em **ingressar**.  

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela ingressar Active Directory, forneça as seguintes informações, com base nos serviços de domínio que você deseja usar:  

    Para obter informações específicas sobre os serviços de domínio que você usa, consulte [decidir quais serviços de domínio usar](#decide-which-domain-services-to-use). 

    * **DNS primário**  
        Esse é o DNS necessário para as operações de autenticação SMB e de ingresso no domínio do Active Directory. 
    * **DNS secundário**   
        Esse é o servidor DNS secundário para garantir que os serviços de nome redundantes. 
    * **Nome de domínio DNS do AD**  
        Esse é o nome de domínio de seu Active Directory Domain Services que você deseja unir.
    * **Nome do site do AD**  
        Esse é o nome do site ao qual a descoberta do controlador de domínio será limitada.
    * **Prefixo do servidor SMB (conta do computador)**  
        Esse é o prefixo de nomenclatura para a conta do computador no Active Directory que Azure NetApp Files será usado para a criação de novas contas.

        Por exemplo, se o padrão de nomenclatura que sua organização usa para servidores de arquivos for NAS-01, NAS-02..., NAS-045, você digitaria "NAS" para o prefixo. 

        O serviço criará contas de computador adicionais no Active Directory conforme necessário.

    * **Caminho da unidade organizacional**  
        Esse é o caminho LDAP para a UO (unidade organizacional) em que as contas de máquina do servidor SMB serão criadas. Ou seja, OU = segundo nível, OU = primeiro nível. 

        Se você estiver usando Azure NetApp Files com Azure Active Directory Domain Services, o caminho da unidade organizacional `OU=AADDC Computers` será quando você configurar Active Directory para sua conta do NetApp.
        
    * Credenciais, incluindo seu **nome de usuário** e **senha**

    ![Ingressar Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Ingressar**.  

    A conexão Active Directory que você criou é exibida.

    ![Conexões Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Você pode editar os campos de nome de usuário e senha depois de salvar a conexão de Active Directory. Nenhum outro valor pode ser editado depois de salvar a conexão. Se precisar alterar outros valores, primeiro exclua todos os volumes SMB implantados e exclua e recrie a conexão de Active Directory.

## <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique na folha **volumes** da folha pools de capacidade. 

    ![Navegar até volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    A janela criar um volume é exibida.

3. Na janela criar um volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        Um nome de volume deve ser exclusivo em cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.   

        Você não pode `default` usar como o nome do volume.

    * **Pool de capacidade**  
        Especifique o pool de capacidade no qual você deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (VNet) da qual você deseja acessar o volume.  

        A VNet que você especificar deve ter uma sub-rede delegada para Azure NetApp Files. O serviço de Azure NetApp Files pode ser acessado somente da mesma VNet ou de uma VNet que está na mesma região que o volume por meio de emparelhamento VNet. Você também pode acessar o volume de sua rede local por meio da rota expressa.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **criar nova** na página criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada VNet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo** e preencha as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a conexão **Active Directory** na lista suspensa.
    * Especifique o nome do volume compartilhado no **nome do compartilhamento**.

    ![Especificar protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **examinar + criar** para examinar os detalhes do volume.  Em seguida, clique em **criar** para criar o volume SMB.

    O volume que você criou aparece na página volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="control-access-to-an-smb-volume"></a>Controlar o acesso a um volume SMB  

O acesso a um volume SMB é gerenciado por meio de permissões.  

### <a name="share-permissions"></a>Permissões de compartilhamento  

Por padrão, um novo volume tem as permissões de compartilhamento **todos/controle total** . Os membros do grupo Admins. do domínio podem alterar as permissões de compartilhamento usando o gerenciamento do computador na conta do computador que é usada para o volume de Azure NetApp Files.

![Caminho](../media/azure-netapp-files/smb-mount-path.png) 
![de montagem do SMB definir permissões de compartilhamento](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Permissões de arquivo e pasta NTFS  

Você pode definir permissões para um arquivo ou pasta usando a guia **segurança** das propriedades do objeto no cliente SMB do Windows.
 
![Definir permissões de arquivo e pasta](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Próximas etapas  

* [Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites de recursos no Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Perguntas frequentes sobre o SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Saiba mais sobre a integração de rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalar uma nova floresta Active Directory usando CLI do Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
