---
title: Solucionar problemas de bloqueio de contas nos serviços de domínio do Azure AD | Microsoft Docs
description: Saiba como solucionar problemas comuns que fazem com que as contas de usuário sejam bloqueadas nos Serviços de Domínio do Diretório Ativo do Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743457"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de bloqueio de contas com um domínio gerenciado do Azure AD Domain Services

Para evitar repetidas tentativas de login maliciosas, o Azure AD DS bloqueia contas após um limite definido. Esse bloqueio de conta também pode acontecer por acidente sem um incidente de ataque de entrada. Por exemplo, se um usuário digita repetidamente a senha errada ou um serviço tenta usar uma senha antiga, a conta fica bloqueada.

Este artigo de solução de problemas descreve por que os bloqueios de contas acontecem e como você pode configurar o comportamento e como rever auditorias de segurança para solucionar eventos de bloqueio.

## <a name="what-is-an-account-lockout"></a>O que é um bloqueio de conta?

Uma conta de usuário no Azure AD DS é bloqueada quando um limite definido para tentativas de login mal sucedidas foi cumprido. Esse comportamento de bloqueio da conta foi projetado para protegê-lo de repetidas tentativas de login de força bruta que podem indicar um ataque digital automatizado.

**Por padrão, se houver 5 tentativas de senha ruins em 2 minutos, a conta fica bloqueada por 30 minutos.**

Os limites de bloqueio de conta padrão são configurados usando a política de senha de grãofino. Se você tiver um conjunto específico de requisitos, você pode substituir esses limites de bloqueio de conta padrão. No entanto, não é recomendado aumentar os limites de limite para tentar reduzir os bloqueios da conta número. Soluciona ndo a fonte do comportamento de bloqueio da conta primeiro.

### <a name="fine-grained-password-policy"></a>Política de senha de grãofino

As políticas de senha de grão fino (FGPPs) permitem aplicar restrições específicas para políticas de bloqueio de senha e conta a diferentes usuários em um domínio. O FGPP afeta apenas os usuários dentro de um domínio gerenciado pelo Azure AD DS. Usuários de nuvem e usuários de domínio sincronizados no domínio gerenciado do Azure AD DS do Azure AD são afetados apenas pelas políticas de senha dentro do Azure AD DS. Suas contas no Azure AD ou em um diretório local não são impactadas.

As políticas são distribuídas por meio da associação de grupos no domínio gerenciado pelo Azure AD DS, e quaisquer alterações que você fizer são aplicadas no próximo login do usuário. Alterar a diretiva não desbloqueia uma conta de usuário que já está bloqueada.

Para obter mais informações sobre políticas de senha sinuosas e as diferenças entre os usuários criados diretamente no Azure AD DS versus sincronizados no Azure AD, consulte Configurar políticas de bloqueio de [senha e conta][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Razões comuns de bloqueio de contas

As razões mais comuns para uma conta ser bloqueada, sem qualquer intenção ou fatores maliciosos, incluem os seguintes cenários:

* **O usuário se trancou para fora.**
    * Após uma recente alteração de senha, o usuário continuou a usar uma senha anterior? A política de bloqueio de conta padrão de 5 tentativas fracassadas em 2 minutos pode ser causada pelo usuário inadvertidamente retentando uma senha antiga.
* **Há um aplicativo ou serviço que tem uma senha antiga.**
    * Se uma conta for usada por aplicativos ou serviços, esses recursos podem tentar repetidamente entrar usando uma senha antiga. Esse comportamento faz com que a conta seja bloqueada.
    * Tente minimizar o uso da conta em vários aplicativos ou serviços diferentes e registre onde as credenciais são usadas. Se uma senha da conta for alterada, atualize os aplicativos ou serviços associados de acordo.
* **A senha foi alterada em um ambiente diferente e a nova senha ainda não foi sincronizada.**
    * Se uma senha de conta for alterada fora do Azure AD DS, como em um ambiente AD DS on-prem, pode levar alguns minutos para que a mudança de senha seja sincronizada através do Azure AD e no Azure AD DS.
    * Um usuário que tenta entrar em um recurso através do Azure AD DS antes que esse processo de sincronização de senha tenha sido concluído faz com que sua conta seja bloqueada.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Solucionar problemas de bloqueios de contas com auditorias de segurança

Para solucionar problemas quando ocorrem eventos de bloqueio de contas e de onde eles estão vindo, [habilite auditorias de segurança para o Azure AD DS][security-audit-events]. Os eventos de auditoria só são capturados a partir do momento em que você habilitar o recurso. Idealmente, você deve habilitar auditorias de segurança *antes* que haja um problema de bloqueio de conta para solucionar problemas. Se uma conta de usuário tiver repetidamente problemas de bloqueio, você poderá habilitar auditorias de segurança prontas para a próxima vez que a situação ocorrer.

Depois de habilitar auditorias de segurança, as seguintes consultas de amostra mostram como rever *Eventos de bloqueio de contas,* código *4740*.

Veja todos os eventos de bloqueio de conta dos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Veja todos os eventos de bloqueio de conta dos últimos sete dias para a conta chamada *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Veja todos os eventos de bloqueio de contas entre 26 de junho de 2019 às 9:00 da manhã. e 1 de julho de 2019 à meia-noite, ordenado ascender pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre políticas de senha com grãos finos para ajustar os limites de bloqueio da conta, consulte [Configurar políticas de bloqueio de senha e conta][configure-fgpp].

Se você ainda tiver problemas para juntar sua VM ao domínio gerenciado pelo Azure AD DS, [encontre ajuda e abra um ticket de suporte para o Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
