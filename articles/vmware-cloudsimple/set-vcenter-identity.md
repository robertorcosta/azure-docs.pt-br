---
title: Solução Azure VMware by CloudSimple - Configure fontes de identidade do vCenter na Private Cloud
description: Descreve como configurar seu vCenter de nuvem privada para autenticar com o Active Directory para administradores de VMware para acessar o vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564016"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Configure as fontes de identidade do vCenter para usar o Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Sobre as fontes de identidade do VMware vCenter

O VMware vCenter suporta diferentes fontes de identidade para autenticação de usuários que acessam o vCenter.  O cloudsimple private cloud vCenter pode ser configurado para autenticar com o Active Directory para que seus administradores do VMware acessem o vCenter. Quando a configuração estiver concluída, o usuário do **cloudowner** pode adicionar usuários da fonte de identidade ao vCenter.  

Você pode configurar o domínio do Active Directory e os controladores de domínio de qualquer uma das seguintes maneiras:

* Controladores de domínio e domínio do Active Directory executando no local
* Controladores de domínio e domínio do Active Directory em execução no Azure como máquinas virtuais em sua assinatura do Azure
* Novos controladores de domínio e domínio do Active Directory em execução em sua Nuvem Privada
* Serviço de Diretório Ativo do Azure

Este guia explica as tarefas para configurar controladores de domínio e domínio do Active Directory executando no local ou como máquinas virtuais em suas assinaturas.  Se você quiser usar o Azure AD como fonte de identidade, consulte [Use Azure AD como um provedor de identidade para o vCenter no CloudSimple Private Cloud](azure-ad.md) para obter instruções detalhadas na configuração da fonte de identidade.

Antes [de adicionar uma fonte de identidade,](#add-an-identity-source-on-vcenter)aumente temporariamente seus [privilégios do vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Novos usuários devem ser adicionados apenas ao *Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os usuários adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas contas de serviço devem ser adicionadas ao grupo *Administradores* e as contas de serviço não devem ser usadas para fazer login na ui web vSphere.   


## <a name="identity-source-options"></a>Opções de origem de identidade

* [Adicione o Active Directory no local como uma única fonte de identidade de login](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configure o novo diretório ativo em uma nuvem privada](#set-up-new-active-directory-on-a-private-cloud)
* [Configurar diretório ativo no Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Adicionar diretório ativo no local como uma única fonte de identidade de login

Para configurar seu Diretório Ativo no local como uma fonte de identidade de login único, você precisa:

* [Conexão VPN site-a-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) do seu datacenter local para a sua Nuvem Privada.
* IP do servidor DNS no local adicionado ao vCenter e ao Controlador de Serviços de Plataforma (PSC).

Use as informações na tabela a seguir ao configurar o domínio do Active Directory.

| **Opção** | **Descrição** |
|------------|-----------------|
| **Nome** | Nome da fonte de identidade. |
| **Base DN para usuários** | Nome distinto da base para usuários. |
| **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça um endereço IP nesta caixa de texto. |
| **Alias de domínio** | O nome netbios de domínio. Adicione o nome NetBIOS do domínio Active Directory como um alias da fonte de identidade se você estiver usando autenticações SSPI. |
| **Base DN para grupos** | O nome distinto da base para grupos. |
| **URL do servidor principal** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Use o `ldap://hostname:port`  `ldaps://hostname:port`formato ou . A porta é tipicamente 389 para conexões LDAP e 636 para conexões LDAPS. Para implantações de controladores multidomínio do Active Directory, a porta é tipicamente 3268 para LDAP e 3269 para LDAPS.<br><br>Um certificado que estabelece confiança para o ponto final LDAPS do `ldaps://` servidor Active Directory é necessário quando você usa na URL LDAP principal ou secundária. |
| **URL do servidor secundário** | Endereço de um servidor LDAP controlador de domínio secundário que é usado para failover. |
| **Escolher certificado** | Se você quiser usar LDAPS com o servidor LDAP do diretório ativo ou a fonte `ldaps://` de identidade do OpenLDAP Server, um botão Escolher certificado será exibido após digitar a caixa de texto URL. Uma URL secundária não é necessária. |
| **Username** | ID de um usuário no domínio que tenha um mínimo de acesso somente à Base DN para usuários e grupos. |
| **Senha** | Senha do usuário especificado pelo Nome de Usuário. |

Quando você tiver as informações na tabela anterior, você pode adicionar seu Diretório Ativo no local como uma fonte de identidade de login único no vCenter.

> [!TIP]
> Você encontrará mais informações sobre fontes de identidade single-sign-on na página de documentação do [VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Configure um novo diretório ativo em uma nuvem privada

Você pode configurar um novo domínio do Active Directory em sua Nuvem Privada e usá-lo como fonte de identidade para o Single Sign-On.  O domínio Active Directory pode ser parte de uma floresta de diretório ativo existente ou pode ser configurado como uma floresta independente.

### <a name="new-active-directory-forest-and-domain"></a>Nova floresta e domínio do Diretório Ativo

Para configurar uma nova floresta e domínio do Active Directory, você precisa:

* Uma ou mais máquinas virtuais executando o Microsoft Windows Server para usar como controladores de domínio para a nova floresta e domínio do Active Directory.
* Uma ou mais máquinas virtuais executando o serviço DNS para resolução de nomes.

Consulte [Instalar uma floresta de diretórios ativos do Novo Servidor Windows 2012](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) para obter etapas detalhadas.

> [!TIP]
> Para alta disponibilidade de serviços, recomendamos a configuração de vários controladores de domínio e servidores DNS.

Depois de configurar a floresta e o domínio do Active Directory, você pode [adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Novo domínio do Diretório Ativo em uma floresta de diretório ativo existente

Para configurar um novo domínio do Active Directory em uma floresta de diretórioativo ativo existente, você precisa:

* Conexão VPN site-to-site com a localização da floresta do Active Directory.
* Servidor DNS para resolver o nome da floresta de diretórios ativos existentes.

Consulte Instalar um novo domínio de [criação ou árvore do Diretório Ativo do Windows Server 2012](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) para obter etapas detalhadas.

Depois de configurar o domínio Active Directory, você pode [adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Configurar o Active Directory no Azure

O Active Directory em execução no Azure é semelhante ao Active Directory em execução no local.  Para configurar o Active Directory em execução no Azure como uma fonte de identidade single de login no vCenter, o servidor vCenter e o PSC devem ter conectividade de rede com a Rede Virtual Do Azure, onde os serviços do Active Directory estão sendo executados.  Você pode estabelecer essa conectividade usando [o Azure Virtual Network Connection usando](azure-expressroute-connection.md) o ExpressRoute da rede virtual Azure, onde os Serviços de Diretório Ativo estão sendo executados para o CloudSimple Private Cloud.

Depois que a conexão de rede for estabelecida, siga as etapas do [Add On-Premises Active Directory como uma única fonte de identidade de login](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) para adicioná-la como fonte de identidade.  

## <a name="add-an-identity-source-on-vcenter"></a>Adicione uma fonte de identidade no vCenter

1. [Aumente privilégios](escalate-private-cloud-privileges.md) em sua Nuvem Privada.

2. Faça login no vCenter para sua Nuvem Privada.

3. Selecione **Administração de > doméstico**.

    ![Administração](media/OnPremAD01.png)

4. Selecione **Sinal único na configuração >**.

    ![Logon Único](media/OnPremAD02.png)

5. Abra a guia **'Fontes de identidade'** e clique **+** para adicionar uma nova fonte de identidade.

    ![Fontes de identidade](media/OnPremAD03.png)

6. Selecione **o Active Directory como um servidor LDAP** e clique **em Next**.

    ![Active Directory](media/OnPremAD04.png)

7. Especifique os parâmetros de origem de identidade para o ambiente e clique **em Next**.

    ![Active Directory](media/OnPremAD05.png)

8. Revise as configurações e clique **em Terminar**.
