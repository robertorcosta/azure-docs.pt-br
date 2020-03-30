---
title: Corrigir erros de diretório incompatíveis nos serviços de domínio do Azure AD | Microsoft Docs
description: Saiba o que significa um erro de diretório incompatível e como resolvê-lo nos Serviços de Domínio Ad do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613393"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolva erros de diretórios incompatíveis para domínios gerenciados existentes do Azure AD Domain Services

Se um domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS) mostrar á sofrência de um erro de inquilino incompatível, você não poderá administrar o domínio gerenciado até ser resolvido. Esse erro ocorre se a rede virtual azure subjacente for transferida para um diretório AD diferente do Azure.

Este artigo explica por que o erro ocorre e como resolvê-lo.

## <a name="what-causes-this-error"></a>O que causa esse erro?

Um erro de diretório incompatível acontece quando um domínio gerenciado pelo Azure AD DS e uma rede virtual pertencem a dois inquilinos Azure AD diferentes. Por exemplo, você pode ter um domínio gerenciado pelo Azure AD DS chamado *aaddscontoso.com* que é executado no inquilino Azure AD da Contoso. No entanto, a rede virtual Azure para domínio gerenciado faz parte do inquilino Do AD Do Fabrikam Azure.

O Azure usa o RBAC (Role-Based Access Control, controle de acesso baseado em função) para limitar o acesso aos recursos. Quando você habilita o Azure AD DS em um inquilino Azure AD, os hashes de credenciais são sincronizados com o domínio gerenciado. Esta operação exige que você seja um administrador de inquilinos para o diretório Azure AD, e o acesso às credenciais deve ser controlado. Para implantar recursos em uma rede virtual do Azure e controlar o tráfego, você deve ter privilégios administrativos na rede virtual na qual você implanta o Azure AD DS.

Para que o RBAC funcione de forma consistente e garanta o acesso a todos os recursos que o Azure AD DS usa, o domínio gerenciado e a rede virtual devem pertencer ao mesmo inquilino do Azure AD.

As regras a seguir se aplicam no ambiente do Resource Manager:

- Um diretório do Azure AD pode ter várias assinaturas do Azure.
- Uma assinatura do Azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerenciado do Azure AD Domain Services fica habilitado para um diretório do Azure AD.
- Um domínio gerenciado do Azure AD Domain Services pode ser habilitado em uma rede virtual pertencente a qualquer uma das assinaturas do Azure no mesmo locatário do Azure AD.

### <a name="valid-configuration"></a>Configuração válida

No cenário de implantação a seguir, o domínio gerenciado do Contoso Azure AD DS está habilitado no inquilino Contoso Azure AD. O domínio gerenciado é implantado em uma rede virtual que pertence a uma assinatura do Azure de propriedade do inquilino AD Contoso Azure. Tanto o domínio gerenciado quanto a rede virtual pertencem ao mesmo inquilino do Azure AD. Esta configuração de exemplo é válida e totalmente suportada.

![Configuração de inquilino AD DS valid com o domínio gerenciado e a parte de rede virtual do mesmo inquilino Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configuração de locatários incompatíveis

Neste cenário de implantação de exemplo, o domínio gerenciado do Contoso Azure AD DS está habilitado no inquilino Contoso Azure AD. No entanto, o domínio gerenciado é implantado em uma rede virtual que pertence a uma assinatura do Azure de propriedade do inquilino AD Do Fabrikam Azure. O domínio gerenciado e a rede virtual pertencem a dois diferentes inquilinos do Azure AD. Esta configuração de exemplo é um inquilino incompatível e não é suportada. A rede virtual deve ser movida para o mesmo inquilino Azure AD que o domínio gerenciado.

![Configuração de locatários incompatíveis](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Resolver erro de inquilino incompatível

As duas opções a seguir resolvem o erro de diretório incompatível:

* [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) do diretório Azure AD existente. [Crie um domínio gerenciado pelo Azure AD DS](tutorial-create-instance.md) de substituição no mesmo diretório Azure AD que a rede virtual deseja usar. Quando estiver pronto, junte todas as máquinas previamente unidas ao domínio excluído ao domínio gerenciado recriado.
* [Mova a assinatura do Azure](../cost-management-billing/manage/billing-subscription-transfer.md) contendo a rede virtual para o mesmo diretório Azure AD do domínio gerenciado pelo Azure AD DS.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre problemas de solução de problemas com o Azure AD DS, consulte o [guia de solução de problemas](troubleshoot.md).
