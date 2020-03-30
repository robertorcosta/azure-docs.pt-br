---
title: Resolver alertas de grupo de segurança de rede no Azure AD DS | Microsoft Docs
description: Saiba como solucionar e resolver alertas de configuração do grupo de segurança da rede para os Serviços de Domínio do Diretório Ativo do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257997"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: alertas de configuração de rede em Serviços de Domínio do Diretório Ativo do Azure

Para permitir que aplicativos e serviços se comuniquem corretamente com o Azure Active Directory Domain Services (Azure AD DS), portas de rede específicas devem estar abertas para permitir que o tráfego flua. No Azure, você controla o fluxo de tráfego usando grupos de segurança de rede. O estado de saúde de um domínio gerenciado pelo Azure AD DS mostra um alerta se as regras necessárias do grupo de segurança da rede não estiverem em vigor.

Este artigo ajuda você a entender e resolver alertas comuns para problemas de configuração de grupos de segurança de rede.

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: erro de rede

### <a name="alert-message"></a>Mensagem de alerta

*A Microsoft não consegue acessar os controladores de domínio para este domínio gerenciado. Isso pode acontecer se um grupo de segurança de rede (NSG) configurado em sua rede virtual bloquear o acesso ao domínio gerenciado. Outra possível razão é se há uma rota definida pelo usuário que bloqueia o tráfego de entrada da internet.*

As regras inválidas do grupo de segurança de rede são a causa mais comum de erros de rede para o Azure AD DS. O grupo de segurança de rede para a rede virtual deve permitir o acesso a portas e protocolos específicos. Se essas portas estiverem bloqueadas, a plataforma Azure não poderá monitorar ou atualizar o domínio gerenciado. A sincronização entre o diretório Azure AD e o domínio gerenciado do Azure AD DS também é impactada. Certifique-se de manter as portas padrão abertas para evitar interrupções no serviço.

## <a name="default-security-rules"></a>Regras de segurança padrão

As seguintes regras de segurança padrão de entrada e saída são aplicadas ao grupo de segurança de rede para um domínio gerenciado pelo Azure AD DS. Essas regras mantêm o Azure AD DS seguro e permitem que a plataforma Azure monitore, gerencie e atualize o domínio gerenciado. Você também pode ter uma regra adicional que permite o tráfego de entrada se você [configurar LDAP seguro][configure-ldaps].

### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Prioridade | Nome | Porta | Protocolo | Fonte | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Qualquer | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Qualquer | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Qualquer | Allow |
| 65000    | AllVnetInBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Qualquer | Qualquer | AzureLoadBalancer | Qualquer | Allow |
| 65500    | DenyAllInBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Prioridade | Nome | Porta | Protocolo | Fonte | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | PermitirAzureLoadBalancerOutBound | Qualquer | Qualquer |  Qualquer | Internet | Allow |
| 65500    | DenyAllOutBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

>[!NOTE]
> O Azure AD DS precisa de acesso irrestrito à rede virtual. Não recomendamos que você crie regras adicionais que restrinjam o acesso de saída para a rede virtual.

## <a name="verify-and-edit-existing-security-rules"></a>Verifique e edite as regras de segurança existentes

Para verificar as regras de segurança existentes e certificar-se de que as portas padrão estão abertas, complete as seguintes etapas:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao seu domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Na **página Visão geral,** as regras de segurança de entrada e saída existentes são mostradas.

    Revise as regras de entrada e saída e compare com a lista de regras exigidas na seção anterior. Se necessário, selecione e exclua as regras personalizadas que bloqueiem o tráfego necessário. Se alguma das regras necessárias estiver faltando, adicione uma regra na próxima seção.

    Depois de adicionar ou excluir regras para permitir o tráfego necessário, o Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="add-a-security-rule"></a>Adicionar uma regra de segurança

Para adicionar uma regra de segurança ausente, complete as seguintes etapas:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao seu domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Em **Configurações** no painel à esquerda, clique em Regras de *segurança de entrada* ou regras de segurança de *saída,* dependendo de qual regra você precisa adicionar.
1. Selecione **Adicionar,** em seguida, criar a regra necessária com base na porta, protocolo, direção, etc. Quando estiver pronto, selecione **OK**.

Leva alguns momentos para que a regra de segurança seja adicionada e exibida na lista.

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
