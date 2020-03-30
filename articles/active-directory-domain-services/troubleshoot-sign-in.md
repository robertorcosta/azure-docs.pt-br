---
title: Solucionar problemas em problemas nos Serviços de Domínio azure AD | Microsoft Docs
description: Saiba como solucionar problemas do usuário em problemas e erros nos Serviços de Domínio do Diretório Ativo do Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612736"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de login de conta com um domínio gerenciado do Azure AD Domain Services

As razões mais comuns para uma conta de usuário que não pode fazer login em um domínio gerenciado pelo Azure AD DS incluem os seguintes cenários:

* [A conta ainda não está sincronizada no Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [O Azure AD DS não tem os hashes de senha para deixar a conta entrar.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A conta está bloqueada.](#the-account-is-locked-out)

> [!TIP]
> O Azure AD DS não pode sincronizar em credenciais para contas que são externas ao inquilino Azure AD. Os usuários externos não podem fazer login no domínio gerenciado pelo Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A conta ainda não está sincronizada no Azure AD DS

Dependendo do tamanho do seu diretório, pode levar um tempo para que contas de usuário e hashes credenciais estejam disponíveis no Azure AD DS. Para grandes diretórios, esta sincronização inicial unidirecional do Azure AD pode levar algumas horas, e até um dia ou dois. Certifique-se de esperar o suficiente antes de tentar novamente a autenticação.

Para ambientes híbridos que o usuário Azure AD Connect para sincronizar dados de diretório no local no Azure AD, certifique-se de executar a versão mais recente do Azure AD Connect e configurar [o Azure AD Connect para executar uma sincronização completa depois de ativar o Azure AD DS][azure-ad-connect-phs]. Se você desativar o Azure AD DS e, em seguida, reativar, você terá que seguir essas etapas novamente.

Se você continuar a ter problemas com contas que não sincronizam através do Azure AD Connect, reinicie o Azure AD Sync Service. A partir do computador com o Azure AD Connect instalado, abra uma janela de prompt de comando e execute os seguintes comandos:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS não tem os hashes de senha

O Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambientes híbridos com sincronização no local

Para ambientes híbridos que usam o Azure AD Connect para sincronizar a partir de um ambiente AD DS no local, você pode gerar e sincronizar localmente os hashes de senha NTLM ou Kerberos necessários no Azure AD. Depois de criar o domínio gerenciado pelo Azure AD DS, [habilite a sincronização de hash de senha para os Serviços de Domínio do Diretório Ativo do Azure][azure-ad-connect-phs]. Sem completar essa etapa de sincronização de hash de senha, você não pode entrar em uma conta usando o Azure AD DS. Se você desativar o Azure AD DS e, em seguida, reativar, você terá que seguir essas etapas novamente.

Para obter mais informações, consulte [Como funciona a sincronização de hash de senha para o Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambientes somente em nuvem sem sincronização no local

Os domínios gerenciados pelo Azure AD DS sem sincronização no local, apenas contas no Azure AD, também precisam gerar os hashes de senha NTLM ou Kerberos necessários. Se uma conta somente na nuvem não puder fazer login, um processo de alteração de senha será concluído com sucesso para a conta depois de habilitar o Azure AD DS?

* **Não, a senha não foi alterada.**
    * [Altere a senha da conta][enable-user-accounts] para gerar os hashes de senha necessários e aguarde 15 minutos antes de tentar entrar novamente.
    * Se você desativar o Azure AD DS e, em seguida, reativar, cada conta deve seguir os passos novamente para alterar sua senha e gerar os hashes de senha necessários.
* **Sim, a senha foi alterada.**
    * Tente entrar usando o formato *UPN,* como `driley@aaddscontoso.com`, em vez `AADDSCONTOSO\deeriley`do formato *SAMAccountName* como .
    * O *SAMAccountName* pode ser gerado automaticamente para usuários cujo prefixo UPN é excessivamente longo ou é o mesmo que outro usuário no domínio gerenciado. O formato *UPN* é garantido para ser único dentro de um inquilino Azure AD.

## <a name="the-account-is-locked-out"></a>A conta está bloqueada

Uma conta de usuário no Azure AD DS é bloqueada quando um limite definido para tentativas de login mal sucedidas foi cumprido. Esse comportamento de bloqueio da conta foi projetado para protegê-lo de repetidas tentativas de login de força bruta que podem indicar um ataque digital automatizado.

Por padrão, se houver 5 tentativas de senha ruins em 2 minutos, a conta fica bloqueada por 30 minutos.

Para obter mais informações e como resolver problemas de bloqueio de contas, consulte [Problemas de bloqueio de contas no Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas para juntar sua VM ao domínio gerenciado pelo Azure AD DS, [encontre ajuda e abra um ticket de suporte para o Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
