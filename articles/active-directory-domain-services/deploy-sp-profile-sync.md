---
title: Habilitar o serviço de perfil do usuário SharePoint com o Azure AD DS | Microsoft Docs
description: Saiba como configurar um domínio gerenciado do Azure Active Directory Domain Services para suportar a sincronização de perfil do SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613860"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configure os serviços de domínio do diretório ativo do Azure para suportar a sincronização do perfil do usuário para o SharePoint Server

O SharePoint Server inclui um serviço para sincronizar perfis de usuários. Esse recurso permite que os perfis de usuários sejam armazenados em um local central e acessíveis em vários sites e fazendas do SharePoint. Para configurar o serviço de perfil de usuário do SharePoint Server, as permissões apropriadas devem ser concedidas em um domínio gerenciado pelo Azure Active Directory Domain Services (Azure AD DS). Para obter mais informações, consulte [a sincronização do perfil do usuário no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo mostra como configurar o Azure AD DS para permitir o serviço de sincronização de perfil de usuário do SharePoint Server.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é acompanhada pelo domínio gerenciado pelo Azure AD DS.
    * Se necessário, complete o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.
* Uma conta de serviço SharePoint para o serviço de sincronização de perfil do usuário.
    * Se necessário, consulte [Plano para contas administrativas e de serviço no SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Visão geral das contas de serviço

Em um domínio gerenciado pelo Azure AD DS, existe um grupo de segurança chamado **AAD DC Service Accounts** como parte da unidade organizacional *usuários* (OU). Os membros desse grupo de segurança recebem os seguintes privilégios:

- **Replicar privilégio Alterações de diretório** na raiz do DSE.
- **Replicar configuração Altera** privilégio no`cn=configuration` contexto de nomeação de *configuração* (contêiner).

O grupo de segurança **Contas de Serviço AAD DC** também é um membro do grupo integrado **Pré-Windows 2000 Compatible Access**.

Quando adicionado a esse grupo de segurança, a conta de serviço do serviço de sincronização de perfil de usuário do SharePoint Server recebe os privilégios necessários para funcionar corretamente.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Habilite o suporte para sincronização do perfil do usuário do SharePoint Server

A conta de serviço do SharePoint Server precisa de privilégios adequados para replicar alterações no diretório e deixar que a sincronização do perfil do usuário do SharePoint Server funcione corretamente. Para fornecer esses privilégios, adicione a conta de serviço usada para sincronização do perfil de usuário do SharePoint ao grupo **Contas de Serviço AAD DC.**

A partir do vm de gerenciamento azure AD DS, complete as seguintes etapas:

> [!NOTE]
> Para editar a adesão ao grupo em um domínio gerenciado pelo Azure AD DS, você deve estar conectado a uma conta de usuário que seja membro do grupo *Administradores AAD DC.*

1. Na tela Iniciar, selecione **Ferramentas Administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para gerenciar a adesão ao grupo, selecione **Centro Administrativo de Diretório Ativo** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha o domínio gerenciado do Azure AD DS, como *aaddscontoso.com*. Uma lista de UOs e recursos existentes é mostrada.
1. Selecione o OU **usuários** e escolha o grupo de segurança *Contas de Serviço AAD DC.*
1. Selecione **Membros**e escolha **Adicionar...**.
1. Digite o nome da conta de serviço SharePoint e selecione **OK**. No exemplo a seguir, a conta de serviço DoSharePoint é chamada *spadmin*:

    ![Adicione a conta de serviço Do SharePoint ao grupo de segurança Contas de Serviço AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [as permissões do Grant Active Directory Domain Services para sincronização de perfil no SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
