---
title: Contas de serviço gerenciadas em grupo para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como criar uma conta de serviço gerenciado em grupo (gMSA) para uso com domínios gerenciados do Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5955f52cda73630f371a46f83ac0fb9a252b80e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655477"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Crie uma conta de serviço gerenciado em grupo (gMSA) no Azure AD Domain Services

Aplicativos e serviços muitas vezes precisam de uma identidade para se autenticarcom com outros recursos. Por exemplo, um serviço web pode precisar autenticar com um serviço de banco de dados. Se um aplicativo ou serviço tiver várias instâncias, como uma fazenda de servidores web, criar e configurar manualmente as identidades desses recursos, será demorado.

Em vez disso, uma conta de serviço gerenciado em grupo (gMSA) pode ser criada no domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS). O Sistema Operacional Windows gerencia automaticamente as credenciais de um gMSA, o que simplifica o gerenciamento de grandes grupos de recursos.

Este artigo mostra como criar um gMSA em um domínio gerenciado pelo Azure AD DS usando o Azure PowerShell.

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

## <a name="managed-service-accounts-overview"></a>Visão geral das contas de serviço gerenciadas

Uma conta de serviço gerenciado autônomo (sMSA) é uma conta de domínio cuja senha é gerenciada automaticamente. Essa abordagem simplifica a gestão do nome principal do serviço (SPN) e permite a gestão delegada para outros administradores. Você não precisa criar e girar manualmente credenciais para a conta.

Uma conta de serviço gerenciado em grupo (gMSA) fornece a mesma simplificação de gerenciamento, mas para vários servidores no domínio. Um gMSA permite que todas as instâncias de um serviço hospedado em uma fazenda de servidores usem o mesmo principal de serviço para que protocolos de autenticação mútua funcionem. Quando um gMSA é usado como diretor de serviço, o sistema operacional Windows novamente gerencia a senha da conta em vez de depender do administrador.

Para obter mais informações, consulte a [visão geral das contas de serviço gerenciadas em grupo (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Usando contas de serviço no Azure AD DS

Como os domínios gerenciados do Azure AD DS são bloqueados e gerenciados pela Microsoft, existem algumas considerações ao usar contas de serviço:

* Crie contas de serviço em unidades organizacionais personalizadas (OU) no domínio gerenciado.
    * Você não pode criar uma conta de serviço nos *OUs de Usuários AADDC* ou *AADDC.*
    * Em vez disso, [crie um OU personalizado][create-custom-ou] no domínio gerenciado pelo Azure AD DS e, em seguida, crie contas de serviço nesse OU personalizado.
* A chave raiz dos Principais Serviços de Distribuição (KDS) é pré-criada.
    * A chave raiz KDS é usada para gerar e recuperar senhas para gMSAs. No Azure AD DS, a raiz KDS é criada para você.
    * Você não tem privilégios para criar outro ou visualizar a tecla padrão, a tecla raiz KDS.

## <a name="create-a-gmsa"></a>Criar uma gMSA

Primeiro, crie um OU personalizado usando o cmdlet [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Para obter mais informações sobre como criar e gerenciar OUs personalizados, consulte [OUs personalizados no Azure AD DS][create-custom-ou].

> [!TIP]
> Para concluir essas etapas para criar um gMSA, [use sua VM de gerenciamento][tutorial-create-management-vm]. Esta VM de gerenciamento já deve ter os cmdlets AD PowerShell necessários e conexão com o domínio gerenciado.

O exemplo a seguir cria um OU personalizado chamado *myNewOU* no domínio gerenciado do Azure AD DS chamado *aaddscontoso.com*. Use seu próprio OU e nome de domínio gerenciado:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Agora crie um gMSA usando o cmdlet [New-ADServiceAccount.][New-ADServiceAccount] Os seguintes parâmetros de exemplo são definidos:

* **-O nome** está definido como *WebFarmSvc*
* **-Parâmetro de** caminho especifica o OU personalizado para o gMSA criado na etapa anterior.
* As entradas dns e os principais nomes do serviço estão definidos para *WebFarmSvc.aaddscontoso.com*
* Os diretores do *AADDSCONTOSO-SERVER$* podem recuperar a senha usando a identidade.

Especifique seus próprios nomes e nomes de domínio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Os aplicativos e serviços agora podem ser configurados para usar o gMSA conforme necessário.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre gMSAs, consulte [Como começar com contas de serviço gerenciadas em grupo][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
