---
title: Criar um volume de SMB para arquivos Do Azure NetApp | Microsoft Docs
description: Descreve como criar um volume de SMB para arquivos Do Azure NetApp.
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668012"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files suporta volumes NFS e SMBv3. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume SMBv3. Se você quiser criar um volume NFS, consulte [Criar um volume NFS para arquivos do Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configure um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para conexões de diretório ativo

 Você precisa criar conexões do Active Directory antes de criar um volume smb. Os requisitos para conexões de Diretório Ativo são os seguintes: 

* A conta de administração que você usa deve ter a capacidade de criar contas de máquina no caminho da unidade organizacional (OU) que você especificará.  

* As portas adequadas devem ser abertas no servidor AD (Windows Active Directory) aplicável.  
    As portas necessárias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços web de anúncios    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta Echo    |
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

* A topologia do site para os Serviços de Domínio de Diretório Ativo direcionados deve aderir às práticas recomendadas, em particular o Azure VNet onde o Azure NetApp Files é implantado.  

    O espaço de endereço para a rede virtual onde o Azure NetApp Files é implantado deve ser adicionado a um site do Active Directory novo ou existente (onde um controlador de domínio acessível pelo Azure NetApp Files é). 

* Os servidores DNS especificados devem ser acessíveis a partir da [sub-rede delegada](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) dos Arquivos Azure NetApp.  

    Consulte [Diretrizes para o planejamento da rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para topoologias de rede suportadas.

    Os NSGs (Network Security Groups, grupos de segurança de rede) e firewalls devem ter regras configuradas adequadamente para permitir solicitações de tráfego Active Directory e DNS. 

* A sub-rede delegada Azure NetApp Files deve ser capaz de alcançar todos os controladores de domínio do Active Directory Domain Services (ADDS) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, a interrupção do serviço pode ocorrer.  

    Se você tiver controladores de domínio inalcançáveis pela sub-rede delegada Azure NetApp Files, você pode especificar um site do Active Directory durante a criação da conexão Active Directory.  O Azure NetApp Files precisa se comunicar apenas com controladores de domínio no site onde está o espaço de endereço delegado do Azure NetApp.

    Consulte [Projetar a topologia do site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre sites e serviços de anúncios. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Consulte Azure NetApp Files [SMB FAQs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) sobre informações adicionais de Anúncios. 

## <a name="decide-which-domain-services-to-use"></a>Decida quais serviços de domínio usar 

O Azure NetApp Files suporta [os AADDS (Active Directory Domain Services, serviços de domínio](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) de diretório ativo) e aaadds (AADDS) do Active Directory Services (AADDS) para conexões AD.  Antes de criar uma conexão AD, você precisa decidir se deve usar ADDS ou AADDS.  

Para obter mais informações, consulte [Compare serviços de domínio de diretório ativo autogerenciados, diretório ativo do Azure e serviços de domínio do diretório ativo gerenciados do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Você pode usar o escopo de [sites e serviços de diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) preferido para arquivos do Azure NetApp. Essa opção permite leituras e gravações para controladores de domínio do Active Directory Domain Services (ADDS) que estão [acessíveis pelo Azure NetApp Files](azure-netapp-files-network-topologies.md). Também impede que o serviço se comunique com controladores de domínio que não estejam no site de Sites e Serviços de Diretório Ativo especificado. 

Para encontrar o nome do seu site ao usar ADDS, você pode entrar em contato com o grupo administrativo em sua organização que é responsável pelos Serviços de Domínio do Diretório Ativo. O exemplo abaixo mostra o plugin Sites e Serviços de Diretório Ativo onde o nome do site é exibido: 

![Serviços e Sites do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Quando você configura uma conexão AD para Arquivos Azure NetApp, você especifica o nome do site no escopo para o campo Nome do site do **AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para a configuração e orientação do Azure Active Directory Domain Services (AADDS), consulte [a documentação dos serviços de domínio do Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/).

Considerações adicionais do AADDS se aplicam aos arquivos do Azure NetApp: 

* Certifique-se de que o VNet ou a sub-rede onde o AADDS está implantado está na mesma região do Azure que a implantação do Azure NetApp Files.
* Se você usar outro VNet na região onde o Azure NetApp Files é implantado, você deve criar um peering entre os dois VNets.
* Azure NetApp Files `user` `resource forest` suporta e tipos.
* Para o tipo de `All` sincronização, você pode selecionar ou `Scoped`.   
    Se você `Scoped`selecionar, certifique-se de que o grupo Azure AD correto esteja selecionado para acessar as ações do SMB.  Se você estiver incerto, `All` você pode usar o tipo de sincronização.
* O uso do Enterprise ou do Premium SKU é necessário. O Standard SKU não é suportado.

Ao criar uma conexão Active Directory, observe as seguintes especificidades para AADDS:

* Você pode encontrar informações para **DNS primário,** **DNS secundário**e **nome de domínio AD DNS** no menu AADDS.  
Para servidores DNS, dois endereços IP serão usados para configurar a conexão Active Directory. 
* O caminho da `OU=AADDC Computers`unidade **organizacional** é .  
Esta configuração está configurada nas **Conexões ativas do diretório** em **Conta NetApp**:

  ![Caminho da unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **As** credenciais de nome de usuário podem ser qualquer usuário que seja membro do grupo **Azure AD AD DC Administrators**.


## <a name="create-an-active-directory-connection"></a>Crie uma conexão active directory

1. Em sua conta do NetApp, clique em **conexões do Active Directory**e clique em **Participar**.  

    ![Conexões de diretório ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela Participar do Diretório Ativo, forneça as seguintes informações, com base nos Serviços de domínio que você deseja usar:  

    Para obter informações específicas dos Serviços de Domínio que você usa, consulte [Decidir quais serviços de domínio usar](#decide-which-domain-services-to-use). 

    * **DNS primário**  
        Este é o DNS necessário para as operações de adesão ao domínio Active Directory e à autenticação smb. 
    * **DNS secundário**   
        Este é o servidor DNS secundário para garantir serviços de nome redundantes. 
    * **Nome de domínio do DNS do AD**  
        Este é o nome de domínio dos serviços de domínio do diretório ativo que você deseja participar.
    * **Nome do site do anúncio**  
        Este é o nome do site ao que a descoberta do Controlador de Domínio será limitada.
    * **Prefixo do servidor SMB (conta de computador)**  
        Este é o prefixo de nomeação para a conta da máquina no Active Directory que o Azure NetApp Files usará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que sua organização usa para servidores de arquivos for NAS-01, NAS-02..., NAS-045, então você digitaria "NAS" para o prefixo. 

        O serviço criará contas de máquina adicionais no Active Directory conforme necessário.

    * **Caminho da unidade organizacional**  
        Este é o caminho LDAP para a unidade organizacional (OU) onde as contas da máquina de servidor SMB serão criadas. Ou seja, OU=segundo nível, OU=primeiro nível. 

        Se você estiver usando arquivos do Azure NetApp com serviços de `OU=AADDC Computers` domínio do Diretório Ativo do Azure, o caminho da unidade organizacional é quando você configura o Active Directory para sua conta netapp.
        
    * Credenciais, incluindo seu **nome de usuário** e **senha**

    ![Participe do Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Ingressar**.  

    A conexão Active Directory que você criou é exibida.

    ![Conexões de diretório ativo](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Você pode editar os campos nome de usuário e senha depois de salvar a conexão Active Directory. Nenhum outro valor pode ser editado após salvar a conexão. Se você precisar alterar quaisquer outros valores, primeiro você deve excluir quaisquer volumes de SMB implantados e, em seguida, excluir e recriar a conexão Active Directory.

## <a name="add-an-smb-volume"></a>Adicionar um volume de SMB

1. Clique na **lâmina Volumes** da lâmina Capacity Pools. 

    ![Navegue até Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    A janela Criar um volume é exibida.

3. Na janela Criar um volume, clique em **Criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        Um nome de volume deve ser único dentro de cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.   

        Você não pode `default` usar como o nome do volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade onde deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual Azure (VNet) a partir da qual deseja acessar o volume.  

        O VNet especificado deve ter uma sub-rede delegada aos Arquivos do Azure NetApp. O serviço Azure NetApp Files só pode ser acessado a partir do mesmo VNet ou de um VNet que está na mesma região que o volume através do peering VNet. Você também pode acessar o volume da sua rede local através do Express Route.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não delegou uma sub-rede, você pode clicar em **Criar uma nova** página Criar um Volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada VNet, apenas uma sub-rede pode ser delegada aos Arquivos Do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique **em Protocolo** e complete as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a conexão **Active Directory** na lista de paradas.
    * Especifique o nome do volume compartilhado no **nome Compartilhar**.

    ![Especificar o protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique **em Revisar + Criar** para revisar os detalhes do volume.  Em seguida, clique **em Criar** para criar o volume smb.

    O volume criado é exibido na página Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="next-steps"></a>Próximas etapas  

* [Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Perguntas frequentes sobre SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Conheça a integração de rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instale uma nova floresta de diretórioativo ativo usando o Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
