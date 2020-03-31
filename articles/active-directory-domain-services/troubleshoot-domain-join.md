---
title: Solucionar problemas de adesão ao Azure AD Domain Services | Microsoft Docs
description: Aprenda a solucionar problemas comuns quando tentar se juntar a uma VM ou conectar um aplicativo aos Serviços de Domínio do Azure Active Directory e não pode se conectar ou autenticar ao domínio gerenciado.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299101"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de adesão ao domínio com um domínio gerenciado pelo Azure AD Domain Services

Quando você tenta juntar uma máquina virtual (VM) ou conectar um aplicativo a um domínio gerenciado aAZure Active Directory Domain Services (AD DS), você pode ter um erro que você não pode fazê-lo. Para solucionar problemas de adesão ao domínio, analise em qual dos seguintes pontos você tem um problema:

* Se você não receber um prompt de autenticação, a VM ou aplicativo não poderá se conectar ao domínio gerenciado pelo Azure AD DS.
    * Comece a solucionar [problemas de conectividade para participar de domínios](#connectivity-issues-for-domain-join).
* Se você receber um erro durante a autenticação, a conexão com o domínio gerenciado pelo Azure AD DS será bem sucedida.
    * Comece a solucionar [problemas relacionados a credenciais durante a adesão ao domínio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemas de conectividade para adesão de domínio

Se a VM não conseguir encontrar o domínio gerenciado pelo Azure AD DS, geralmente há um problema de conexão ou configuração de rede. Revise as seguintes etapas de solução de problemas para localizar e resolver o problema:

1. Certifique-se de que a VM está conectada à mesma, ou a uma rede virtual com peered habilitada para OZure AD DS. Caso assim, a VM não pode encontrar e conectar-se ao domínio para participar.
    * Se a VM não estiver conectada à mesma rede virtual, confirme se a conexão de rede virtual ou VPN está *ativa* ou *conectada* para permitir que o tráfego flua corretamente.
1. Tente pingar o domínio usando o nome de domínio do domínio gerenciado `ping aaddscontoso.com`do Azure AD DS, como .
    * Se a resposta de ping falhar, tente pingar os endereços IP para o domínio exibido na página de exibição geral no portal para o domínio gerenciado pelo Azure AD DS, como `ping 10.0.0.4`.
    * Se você puder pingar com sucesso o endereço IP, mas não o domínio, o DNS pode estar configurado incorretamente. Certifique-se de que você configurou os servidores DNS de domínio gerenciados do Azure AD DS para a rede virtual.
1. Tente lavar o cache de resolução de DNS na máquina virtual, como `ipconfig /flushdns`.

### <a name="network-security-group-nsg-configuration"></a>Configuração do Grupo de Segurança de Rede (NSG)

Quando você cria um domínio gerenciado pelo Azure AD DS, um grupo de segurança de rede também é criado com as regras apropriadas para uma operação de domínio bem-sucedida. Se você editar ou criar regras adicionais do grupo de segurança de rede, poderá bloquear involuntariamente as portas necessárias para que o Azure AD DS forneça serviços de conexão e autenticação. Essas regras do grupo de segurança da rede podem causar problemas como sincronização de senha sem preenchimento, usuários que não podem fazer login ou problemas de adesão de domínio.

Se você continuar a ter problemas de conexão, revise as seguintes etapas de solução de problemas:

1. Verifique o estado de saúde do seu domínio gerenciado pelo Azure AD DS no portal Azure. Se você tiver um alerta para *o AADDS001,* uma regra do grupo de segurança da rede está bloqueando o acesso.
1. Revise as [portas necessárias e as regras do grupo de segurança da rede.][network-ports] Certifique-se de que nenhuma regra do grupo de segurança de rede seja aplicada à VM ou à rede virtual que você está conectando a partir do bloqueio dessas portas de rede.
1. Uma vez que quaisquer problemas de configuração do grupo de segurança de rede são resolvidos, o alerta *AADDS001* desaparece da página de saúde em cerca de 2 horas. Com a conectividade de rede agora disponível, tente entrar novamente na VM.

## <a name="credentials-related-issues-during-domain-join"></a>Problemas relacionados a credenciais durante a adesão ao domínio

Se você receber uma caixa de diálogo que solicite credenciais para participar do domínio gerenciado pelo Azure AD DS, a VM poderá se conectar ao domínio usando a rede virtual Do Zure. O processo de adesão ao domínio falha na autenticação do domínio ou na autorização para concluir o processo de adesão ao domínio usando as credenciais fornecidas.

Para solucionar problemas relacionados a credenciais, revise as seguintes etapas de solução de problemas:

1. Tente usar o formato UPN para especificar as credenciais, como `dee@aaddscontoso.onmicrosoft.com`. Certifique-se de que este UPN está configurado corretamente no Azure AD.
    * O *SAMAccountName* para sua conta pode ser gerado automaticamente se houver vários usuários com o mesmo prefixo UPN em seu inquilino ou se o seu prefixo UPN for excessivamente longo. Portanto, o formato *SAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.
1. Tente usar as credenciais de uma conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS para juntar VMs ao domínio gerenciado.
1. Certifique-se de que você [habilitou][enable-password-sync] a sincronização de senha e esperou tempo suficiente para que a sincronização inicial de senha seja concluída.

## <a name="next-steps"></a>Próximas etapas

Para obter uma compreensão mais profunda dos processos do Active Directory como parte da operação de participação em [domínios, consulte Participar e problemas de autenticação][join-authentication-issues].

Se você ainda tiver problemas para juntar sua VM ao domínio gerenciado pelo Azure AD DS, [encontre ajuda e abra um ticket de suporte para o Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
