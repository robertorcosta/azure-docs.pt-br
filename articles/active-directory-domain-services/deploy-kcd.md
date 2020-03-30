---
title: Kerberos constrangeu delegação para a Azure AD Domain Services | Microsoft Docs
description: Saiba como ativar a delegação restrita kerberos (KCD) baseada em recursos em um domínio gerenciado do Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 216fdeca9893f4e290474512617f13382d22890f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614022"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configure a delegação restrita kerberos (KCD) nos serviços de domínio do Diretório Ativo do Azure

À medida que você executa aplicativos, pode haver a necessidade desses aplicativos de acessar recursos no contexto de um usuário diferente. O Active Directory Domain Services (AD DS) suporta um mecanismo chamado *delegação Kerberos* que permite esse caso de uso. Kerberos *restringe a* delegação (KCD) então se baseia neste mecanismo para definir recursos específicos que podem ser acessados no contexto do usuário. Os domínios gerenciados do Azure Active Directory Domain Services (Azure AD DS) são bloqueados com mais segurança do que os ambientes AD DS tradicionais no local, portanto, use um KCD *baseado em recursos* mais seguro.

Este artigo mostra como configurar a delegação kerberos baseada em recursos em um domínio gerenciado pelo Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é acompanhada pelo domínio gerenciado pelo Azure AD DS.
    * Se necessário, complete o tutorial para [criar uma VM do Windows Server e junte-a a um domínio gerenciado][create-join-windows-vm] e [instale as ferramentas de gerenciamento de DS][tutorial-create-management-vm]do AD .
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos constrangeu visão geral da delegação

A delegação de Kerberos permite que uma conta se passe por outra para acessar recursos. Por exemplo, um aplicativo web que acessa um componente web back-end pode se passar por uma conta de usuário diferente quando faz a conexão back-end. A delegação kerberos é insegura, pois não limita os recursos que a conta de representação pode acessar.

A delegação restrita kerberos (KCD) restringe os serviços ou recursos que um servidor ou aplicativo especificado pode conectar ao se passar por outra identidade. O KCD tradicional exige privilégios de administrador de domínio para configurar uma conta de domínio para um serviço e restringe a execução da conta em um único domínio.

O KCD tradicional também tem alguns problemas. Por exemplo, em sistemas operacionais anteriores, o administrador de serviços não tinha uma maneira útil de saber quais serviços front-end delegavam aos serviços de recursos que possuíam. Qualquer serviço front-end que pudesse delegar a um serviço de recursos era um ponto de ataque em potencial. Se um servidor que hospedou um serviço front-end configurado para delegar serviços de recursos foi comprometido, os serviços de recursos também podem ser comprometidos.

Em um domínio gerenciado pelo Azure AD DS, você não tem privilégios de administrador de domínio. Como resultado, o KCD tradicional baseado em conta não pode ser configurado em um Azure AD DS um domínio gerenciado. Em vez disso, o KCD baseado em recursos pode ser usado, o que também é mais seguro.

### <a name="resource-based-kcd"></a>KCD baseado em recursos

O Windows Server 2012 e posteriormente dá aos administradores de serviços a capacidade de configurar delegação restrita para seu serviço. Esse modelo é conhecido como KCD com base em recursos. Com essa abordagem, o administrador de serviços back-end pode permitir ou negar serviços front-end específicos de usar KCD.

A KCD Com base em recursos é configurado usando o PowerShell. Você usa os cmdlets [Set-ADComputer][Set-ADComputer] ou [Set-ADUser,][Set-ADUser] dependendo se a conta de representação é uma conta de computador ou uma conta de usuário/ conta de serviço.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configure o KCD baseado em recursos para uma conta de computador

Neste cenário, vamos supor que você tenha um aplicativo web que seja executado no computador chamado *contoso-webapp.aaddscontoso.com*. O aplicativo web precisa acessar uma API web que é executada no computador chamado *contoso-api.aaddscontoso.com* no contexto dos usuários de domínio. Complete as seguintes etapas para configurar este cenário:

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esse OU personalizado para usuários dentro do domínio gerenciado pelo Azure AD DS.
1. [Junte-se ao domínio das máquinas virtuais][create-join-windows-vm], tanto a que executa o aplicativo web, quanto a que executa a API web, para o domínio gerenciado pelo Azure AD DS. Crie essas contas de computador no OU personalizado da etapa anterior.

    > [!NOTE]
    > O computador conta para o aplicativo web e a API da Web deve estar em um OU personalizado onde você tem permissões para configurar kcd baseado em recursos. Você não pode configurar o KCD baseado em recursos para uma conta de computador no contêiner *AAD DC Computers* incorporado.

1. Finalmente, configure o KCD baseado em recursos usando o cmdlet [Set-ADComputer][Set-ADComputer] PowerShell. A partir de sua VM de gerenciamento de domínio e logado como conta de usuário que é um membro do grupo de *administradores Azure AD DC,* execute os cmdlets a seguir. Forneça seus próprios nomes de computador conforme necessário:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configure o KCD baseado em recursos para uma conta de usuário

Neste cenário, vamos supor que você tenha um aplicativo web que seja executado como uma conta de serviço chamada *appsvc*. O aplicativo web precisa acessar uma API web que é executada como uma conta de serviço chamada *backendsvc* no contexto dos usuários de domínio. Complete as seguintes etapas para configurar este cenário:

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esse OU personalizado para usuários dentro do domínio gerenciado pelo Azure AD DS.
1. [Junte-se às máquinas virtuais][create-join-windows-vm] que executam a API/recurso web backend para o domínio gerenciado pelo Azure AD DS. Crie sua conta de computador dentro da OU personalizada.
1. Crie a conta de serviço (por exemplo, 'appsvc') usada para executar o aplicativo da Web dentro da OU personalizada.

    > [!NOTE]
    > Novamente, a conta do computador para a VM aPI web, e a conta de serviço para o aplicativo web, devem estar em um OU personalizado onde você tem permissões para configurar kcd baseado em recursos. Você não pode configurar o KCD baseado em recursos para contas nos *computadores AAD DC* ou nos contêineres *AAD DC Users.* Isso também significa que você não pode usar contas de usuário sincronizadas do Azure AD para configurar o KCD baseado em recursos. Você deve criar e usar contas de serviço criadas especificamente no Azure AD DS.

1. Finalmente, configure o KCD baseado em recursos usando o [cmdlet Set-ADUser][Set-ADUser] PowerShell. A partir de sua VM de gerenciamento de domínio e logado como conta de usuário que é um membro do grupo de *administradores Azure AD DC,* execute os cmdlets a seguir. Forneça seus próprios nomes de serviço conforme necessário:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como a delegação trabalha nos Serviços de Domínio do Diretório Ativo, consulte [A visão geral da delegação restrita de Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
