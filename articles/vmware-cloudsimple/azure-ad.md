---
title: Solução Azure VMware by CloudSimple - Use o Azure AD como fonte de identidade na Private Cloud
description: Descreve como adicionar o Azure AD como um provedor de identidade em sua Cloud Private Cloud cloud para autenticar usuários que acessam o CloudSimple do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564577"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Use o Azure AD como provedor de identidade para o vCenter no Cloud Simple Private Cloud

Você pode configurar seu CloudSimple Private Cloud vCenter para autenticar com o Azure Active Directory (Azure AD) para que seus administradores do VMware acessem o vCenter. Depois que a única fonte de identificação de login for configurada, o usuário do **cloudowner** pode adicionar usuários da fonte de identidade ao vCenter.  

Você pode configurar o domínio do Active Directory e os controladores de domínio de qualquer uma das seguintes maneiras:

* Controladores de domínio e domínio do Active Directory executando no local
* Controladores de domínio e domínio do Active Directory em execução no Azure como máquinas virtuais em sua assinatura do Azure
* Novos controladores de domínio e domínio do Active Directory em execução em sua Nuvem Privada CloudSimple
* Serviço de Diretório Ativo do Azure

Este guia explica as tarefas necessárias para configurar o Azure AD como fonte de identidade.  Para obter informações sobre o uso do Active Directory ou do Active Directory em execução no Azure, consulte [Configurar fontes de identidade do vCenter para usar o Active Directory](set-vcenter-identity.md) para obter instruções detalhadas na configuração da fonte de identidade.

## <a name="about-azure-ad"></a>Sobre o Azure AD

O Azure AD é o serviço de gerenciamento de identidade e diretório multi-inquilino sinuoso da Microsoft.  O Azure AD fornece um mecanismo de autenticação escalável, consistente e confiável para que os usuários autentiquem e acessem diferentes serviços no Azure.  Ele também fornece serviços LDAP seguros para quaisquer serviços de terceiros para usar o Azure AD como uma fonte de autenticação/identidade.  O Azure AD combina serviços de diretório principal, governança avançada de identidade e gerenciamento de acesso a aplicativos, que podem ser usados para dar acesso à sua Nuvem Privada para usuários que administram a Nuvem Privada.

Para usar o Azure AD como fonte de identidade com o vCenter, você deve configurar os serviços de domínio Azure AD e Azure AD. Siga estas instruções:

1. [Como configurar os serviços de domínio Azure AD e Azure AD](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Como configurar uma fonte de identidade no seu vCenter do Private Cloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Configurar serviços de domínio Azure AD e Azure AD

Antes de começar, você precisará acessar sua assinatura do Azure com privilégios de Administrador Global.  As seguintes etapas dão diretrizes gerais. Os detalhes estão contidos na documentação do Azure.

### <a name="azure-ad"></a>AD do Azure

> [!NOTE]
> Se você já tem a Azure AD, você pode pular esta seção.

1. Configure o Azure AD em sua assinatura conforme descrito na [documentação do Azure AD](../active-directory/fundamentals/get-started-azure-ad.md).
2. Habilite o Azure Active Directory Premium em sua assinatura conforme descrito no [Signup for Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Configure um nome de domínio personalizado e verifique o nome de domínio personalizado conforme descrito em [Adicionar um nome de domínio personalizado ao Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Configure um registro de DNS no seu registrador de domínio com as informações fornecidas no Azure.
    2. Defina o nome de domínio personalizado como o domínio principal.

Você pode configurar opcionalmente outros recursos do Azure AD.  Estes não são necessários para ativar a autenticação do vCenter com o Azure AD.

### <a name="azure-ad-domain-services"></a>Serviços de domínio Azure AD

> [!NOTE]
> Este é um passo importante para habilitar o Azure AD como uma fonte de identidade para o vCenter.  Para evitar problemas, certifique-se de que todas as etapas sejam executadas corretamente.

1. Habilite os serviços de domínio Azure AD conforme descrito nos [serviços de domínio Do Azure Active Directory usando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Configure a rede que será usada pelos serviços de domínio Azure AD conforme descrito em [Ativar serviços de domínio do Diretório Ativo do Azure usando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Configure o Administrator Group para gerenciar os serviços de domínio Azure AD conforme descrito em [Ativar serviços de domínio do diretório ativo do Azure usando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Atualize as configurações de DNS para os serviços de domínio Azure AD conforme descrito em [Ativar serviços de domínio do diretório ativo do Azure](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Se você quiser se conectar a AD pela Internet, configure o registro DNS para o endereço IP público dos serviços de domínio Azure AD para o nome de domínio.
5. Habilite a sincronização de hash de senha para usuários.  Esta etapa permite a sincronização dos hashes de senha necessários para nt LAN Manager (NTLM) e autenticação Kerberos para Azure AD Domain Services. Depois que a sincronização de hashes de senha é configurada, os usuários podem entrar no domínio gerenciado com suas credenciais corporativas. Consulte [Ativar a sincronização de hash de senha para os serviços de domínio do Diretório Ativo do Azure](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Se os usuários somente na nuvem estiverem presentes, eles devem alterar sua senha usando <a href="http://myapps.microsoft.com/" target="_blank">o painel de acesso Azure AD</a> para garantir que os hashes de senha sejam armazenados no formato exigido por NTLM ou Kerberos.  Siga as instruções em [Habilitar a sincronização de hash de senha ao seu domínio gerenciado para contas de usuário somente](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)na nuvem .  Esta etapa deve ser feita para usuários individuais e qualquer novo usuário que seja criado em seu diretório Azure AD usando o portal Azure ou cmdlets Azure AD PowerShell. Os usuários que necessitarem de acesso aos serviços de domínio do Azure AD devem usar o <a href="http://myapps.microsoft.com/" target="_blank">painel de acesso Azure AD</a> e acessar seu perfil para alterar a senha.

        > [!NOTE]
        > Se a sua organização tiver contas de usuário somente na nuvem, todos os usuários que precisarem usar o Azure Active Directory Domain Services deverão alterar suas senhas. Uma conta de usuário somente na nuvem é uma conta que foi criada no diretório do Azure AD usando o portal do Azure ou os cmdlets do Azure AD PowerShell. Essas contas de usuário não são sincronizadas de um diretório local.

    2. Se você estiver sincronizando senhas do diretório ativo no local, siga as etapas na documentação do [Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Configure lDAP seguro em seus Serviços de Domínio de Diretório Ativo do Azure conforme descrito no [Configure Secure LDAP (LDAPS) para um domínio gerenciado do Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Faça upload de um certificado para uso por LDAP seguro conforme descrito no tópico Azure [obtenha um certificado para LDAP seguro](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  O CloudSimple recomenda o uso de um certificado assinado emitido por uma autoridade de certificado para garantir que o vCenter possa confiar no certificado.
    2. Habilite o LDAP seguro como descrito [Habilitar LDAP (LDAPS) seguro para um domínio gerenciado do Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Salve a parte pública do certificado (sem a chave privada) no formato .cer para uso com o vCenter enquanto configura a fonte de identidade.
    4. Se for necessário acesso à Internet aos serviços de domínio do Azure AD, habilite a opção 'Permitir acesso seguro ao LDAP via internet'.
    5. Adicione a regra de segurança de entrada para os serviços de domínio Azure AD NSG para a porta TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Configure uma fonte de identidade no seu vCenter privado da Nuvem

1. [Aumente privilégios](escalate-private-cloud-privileges.md) para o seu VCenter Private Cloud.
2. Coletar os parâmetros de configuração necessários para a configuração da fonte de identidade.

    | **Opção** | **Descrição** |
    |------------|-----------------|
    | **Nome** | Nome da fonte de identidade. |
    | **Base DN para usuários** | Nome distinto da base para usuários.  Para a azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça um endereço IP nesta caixa de texto. |
    | **Alias de domínio** | *(opcional)* O nome netbios de domínio. Adicione o nome NetBIOS do domínio Active Directory como um alias da fonte de identidade se você estiver usando autenticações SSPI. |
    | **Base DN para grupos** | O nome distinto da base para grupos. Para o Azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Exemplo:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL do servidor principal** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Use o `ldaps://hostname:port`formato . A porta é tipicamente 636 para conexões LDAPS. <br><br>Um certificado que estabelece confiança para o ponto final LDAPS do `ldaps://` servidor Active Directory é necessário quando você usa na URL LDAP principal ou secundária. |
    | **URL do servidor secundário** | Endereço de um servidor LDAP controlador de domínio secundário que é usado para failover. |
    | **Escolher certificado** | Se você quiser usar LDAPS com o servidor LDAP do diretório ativo ou a fonte `ldaps://` de identidade do OpenLDAP Server, um botão Escolher certificado será exibido após digitar a caixa de texto URL. Uma URL secundária não é necessária. |
    | **Username** | ID de um usuário no domínio que tenha um mínimo de acesso somente à Base DN para usuários e grupos. |
    | **Senha** | Senha do usuário especificado pelo Nome de Usuário. |

3. Faça login no seu VCenter private cloud depois que os privilégios forem aumentados.
4. Siga as instruções em Adicionar uma fonte de [identidade no vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) usando os valores da etapa anterior para configurar o Azure Active Directory como uma fonte de identidade.
5. Adicione usuários/grupos do Azure AD aos grupos vCenter conforme descrito no tópico VMware [Adicione membros a um grupo de login único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Novos usuários devem ser adicionados apenas ao *Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os usuários adicionados ao grupo *Administradores* serão removidos automaticamente.  Somente contas de serviço devem ser adicionadas ao grupo *Administradores.*

## <a name="next-steps"></a>Próximas etapas

* [Conheça o modelo de permissão do Private Cloud](learn-private-cloud-permissions.md)
