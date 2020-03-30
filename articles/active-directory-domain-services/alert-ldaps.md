---
title: Resolva alertas LDAP seguros nos serviços de domínio Azure AD | Microsoft Docs
description: Saiba como solucionar problemas e resolver alertas comuns com LDAP seguro para serviços de domínio de diretório ativo do Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258045"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas LDAP seguros em serviços de domínio do Diretório Ativo do Azure

Aplicativos e serviços que usam o protocolo de acesso a diretórios leves (LDAP) para se comunicar com o Azure Active Directory Domain Services (Azure AD DS) podem ser [configurados para usar LDAP seguro](tutorial-configure-ldaps.md). Um certificado apropriado e portas de rede necessárias devem estar abertas para que o LDAP seguro funcione corretamente.

Este artigo ajuda você a entender e resolver alertas comuns com acesso LDAP seguro no Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Configuração de rede LDAP segura

### <a name="alert-message"></a>Mensagem de alerta

*O LDAP seguro na internet está habilitado para o domínio gerenciado. No entanto, o acesso à porta 636 não está bloqueado usando um grupo de segurança de rede. Isso pode expor contas de usuário no domínio gerenciado a ataques de força bruta por senha.*

### <a name="resolution"></a>Resolução

Quando você habilita ldap seguro, recomenda-se criar regras adicionais que restrinjam o acesso ldaps de entrada a endereços IP específicos. Essas regras protegem o domínio gerenciado pelo Azure AD DS contra ataques de força bruta. Para atualizar o grupo de segurança da rede para restringir o acesso à porta TCP 636 para LDAP seguro, complete as seguintes etapas:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG,* e selecione **as regras de segurança de entrada**
1. **+ Adicione** uma regra para a porta TCP 636. Se necessário, **selecione Avançado** na janela para criar uma regra.
1. Para **a Fonte,** escolha *Endereços IP* no menu suspenso. Digite os endereços IP de origem que você deseja conceder acesso para tráfego LDAP seguro.
1. Escolha *Any* como **destino**e digite *636* para **as faixas de destino**do porto .
1. Defina o **Protocolo** como *TCP* e a **Ação** para *Permitir*.
1. Especifique a prioridade para a regra e digite um nome como *RestrictLDAPS*.
1. Quando estiver pronto, **selecione Adicionar** para criar a regra.

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

> [!TIP]
> A porta TCP 636 não é a única regra necessária para que o Azure AD DS seja executado sem problemas. Para saber mais, consulte os [grupos de segurança azure AD DS Network e as portas necessárias](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Expiração do certificado LDAP Seguro

### <a name="alert-message"></a>Mensagem de alerta

*O certificado LDAP seguro para o domínio gerenciado expirará em [data]].*

### <a name="resolution"></a>Resolução

Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Aplique o certificado de substituição ao Azure AD DS e distribua o certificado a todos os clientes que se conectarem usando LDAP seguro.

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
