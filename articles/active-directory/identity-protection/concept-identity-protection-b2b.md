---
title: Proteção de identidade e usuários B2B - Diretório Ativo do Azure
description: Usando proteção de identidade com usuários B2B como funciona e limitações conhecidas
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881319"
---
# <a name="identity-protection-and-b2b-users"></a>Usuários do Identity Protection e B2B

Com a colaboração Azure AD B2B, as organizações podem aplicar políticas baseadas em riscos para usuários B2B usando proteção de identidade. Essas políticas serão configuradas de duas maneiras:

- Os administradores podem configurar as políticas incorporadas baseadas em risco de proteção de identidade, que se aplicam a todos os aplicativos, que incluem usuários convidados.
- Os administradores podem configurar suas políticas de Acesso Condicional, usando o risco de login como condição, que inclui usuários convidados.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Como é avaliado o risco para usuários de colaboração B2B

O risco do usuário para usuários de colaboração B2B é avaliado em seu diretório doméstico. O risco de login em tempo real para esses usuários é avaliado no diretório de recursos quando eles tentam acessar o recurso.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitações da Proteção de Identidade para usuários de colaboração B2B

Existem limitações na implementação do Identity Protection para usuários de colaboração B2B em um diretório de recursos devido à sua identidade existente em seu diretório inicial. As principais limitações são as seguintes:

- Se um usuário convidado acionar a política de risco do usuário de Proteção de Identidade para forçar a redefinição de senha, **ele será bloqueado**. Esse bloco é devido à incapacidade de redefinir senhas no diretório de recursos.
- **Os usuários convidados não aparecem no relatório usuários de risco**. Essa perda de visibilidade deve-se à avaliação de risco que ocorre no diretório inicial do usuário B2B.
- Os administradores **não podem descartar ou remediar um usuário de colaboração B2B arriscado** em seu diretório de recursos. Essa perda de funcionalidade deve-se aos administradores do diretório de recursos que não têm acesso ao diretório inicial do usuário B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Por que não posso remediar usuários arriscados de colaboração B2B em meu diretório?

A avaliação de risco e a correção para usuários B2B ocorre em seu diretório residencial. Devido a esse fato, os usuários convidados não aparecem no relatório de usuários arriscados no diretório de recursos e os admins no diretório de recursos não podem forçar uma redefinição de senha segura para esses usuários.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>O que faço se um usuário de colaboração B2B foi bloqueado devido a uma política baseada em riscos na minha organização?

Se um usuário B2B arriscado em seu diretório for bloqueado pela sua política baseada em riscos, o usuário precisará remediar esse risco em seu diretório doméstico. Os usuários podem remediar seu risco realizando uma redefinição de senha segura em seu diretório inicial. Se eles não tiverem a senha de autoatendimento redefinida ativada em seu diretório inicial, eles precisarão entrar em contato com a equipe de TI de sua própria organização para que um administrador descarte manualmente seu risco ou redefinisua senha.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Como faço para evitar que os usuários de colaboração B2B sejam impactados por políticas baseadas em riscos?

A exclusão de usuários B2B das políticas de acesso condicional baseadas em risco da sua organização impedirá que os usuários B2B sejam afetados ou bloqueados por sua avaliação de risco. Para excluir esses usuários B2B, crie um grupo no Azure AD que contenha todos os usuários convidados da sua organização. Em seguida, adicione esse grupo como uma exclusão para as políticas de risco e risco de login incorporadas do usuário de proteção de identidade, bem como quaisquer políticas de acesso condicional que usem o risco de login como condição.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração Azure AD B2B?](../b2b/what-is-b2b.md)
