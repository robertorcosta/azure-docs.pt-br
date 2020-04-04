---
title: Serviços de domínio AD do Azure Seguro | Microsoft Docs
description: Saiba como desativar cifras fracas, protocolos antigos e sincronização de hash de senha NTLM para um domínio gerenciado do Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654713"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Desabilite cifras fracas e sincronização de hash de senha para proteger um domínio gerenciado do Azure AD Domain Services

Por padrão, o Azure Active Directory Domain Services (Azure AD DS) permite o uso de cifras como NTLM v1 e TLS v1. Essas cifras podem ser necessárias para alguns aplicativos legados, mas são consideradas fracas e podem ser desativadas se você não precisar delas. Se você tiver conectividade híbrida no local usando o Azure AD Connect, você também poderá desativar a sincronização de hashes de senha NTLM.

Este artigo mostra como desativar as cifras NTLM v1 e TLS v1 v1 e desativar a sincronização de hash de senha NTLM.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e conecte-se à sua assinatura Do Azure](/powershell/azure/install-az-ps).
    * Certifique-se de fazer login na sua assinatura do Azure usando o cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e conecte-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de fazer login no seu inquilino Azure AD usando o [cmdlet Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Desativar cifras fracas e sincronização de hash de senha NTLM

Para desativar suítes de cifras fracas e sincronização de hash de credencial NTLM, faça login na sua conta do Azure e obtenha o recurso Azure AD DS usando o cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Se você receber um erro usando o comando [Get-AzResource][Get-AzResource] que o recurso *Microsoft.AAD/DomainServices* não existe, [eleve seu acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Em seguida, defina *DomainSecuritySettings* para configurar as seguintes opções de segurança:

1. Desativar o suporte NTLM v1.
2. Desabilitar a sincronização de hashes de senha de NTLM do AD local.
3. Desativar o TLS v1.

> [!IMPORTANT]
> Usuários e contas de serviço não podem executar vínculos simples LDAP se você desativar a sincronização de hash de senha NTLM no domínio gerenciado pelo Azure AD DS. Se você precisar executar obrigações simples de LDAP, não defina a *opção "SyncNtlmPasswords"="Disabled";* opção de configuração de segurança no comando a seguir.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Finalmente, aplique as configurações de segurança definidas no domínio gerenciado pelo Azure AD DS usando o [cmdlet Set-AzResource.][Set-AzResource] Especifique o recurso Azure AD DS desde a primeira etapa e as configurações de segurança da etapa anterior.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Leva alguns momentos para que as configurações de segurança sejam aplicadas ao domínio gerenciado pelo Azure AD DS.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o processo de sincronização, consulte [Como objetos e credenciais são sincronizados em um domínio gerenciado pelo Azure AD DS][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD