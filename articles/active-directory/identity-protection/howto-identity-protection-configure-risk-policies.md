---
title: Políticas de risco - Proteção de identidade do diretório ativo do Azure
description: Habilite e configure políticas de risco no Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75706998"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Como: Configurar e habilitar políticas de risco

Como aprendemos no artigo anterior, as [políticas de Proteção à Identidade](concept-identity-protection-policies.md) temos duas políticas de risco que podemos habilitar em nosso diretório. 

- Política de risco de entrada
- Política de risco do usuário

![Página de visão geral de segurança para habilitar políticas de risco de usuário e login](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas as políticas trabalham para automatizar a resposta às detecções de risco em seu ambiente e permitir que os usuários se auto-remediam quando o risco é detectado. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Pré-requisitos 

Se sua organização quiser permitir que os usuários se auto-remediam quando os riscos forem detectados, os usuários devem ser registrados tanto para redefinição de senha de autoatendimento quanto para autenticação multifatorial do Azure. Recomendamos [habilitar a experiência combinada](../authentication/howto-registration-mfa-sspr-combined.md) de registro de informações de segurança para a melhor experiência. Permitir que os usuários se auto-remediam os leva de volta a um estado produtivo mais rapidamente sem exigir intervenção do administrador. Os administradores ainda podem ver esses eventos e investigá-los após o fato. 

## <a name="choosing-acceptable-risk-levels"></a>Escolhendo níveis de risco aceitáveis

As organizações devem decidir o nível de risco que estão dispostas a aceitar equilibrando a experiência do usuário e a postura de segurança. 

A recomendação da Microsoft é definir o limite da política de risco do usuário para **Alto** e a política de risco de login para **Médio e superior**.

Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários. No entanto, exclui detecções de risco **baixo** e **médio** da diretiva, o que pode não impedir um invasor de explorar uma identidade comprometida. Selecionar um **limite baixo** introduz interrupções adicionais do usuário, mas uma postura de segurança aumentada.

## <a name="exclusions"></a>Exclusões

Todas as políticas permitem excluir usuários, como seu [acesso de emergência ou contas de administrador de vidro sumido](../users-groups-roles/directory-emergency-access.md). As organizações podem determinar que precisam excluir outras contas de políticas específicas com base na forma como as contas são usadas. Todas as exclusões devem ser revistas regularmente para ver se ainda são aplicáveis.

Locais de [rede](../conditional-access/location-condition.md) confiáveis configurados são usados pela Identity Protection em algumas detecções de risco para reduzir falsos positivos.

## <a name="enable-policies"></a>Habilitar políticas

Para habilitar as políticas de risco de risco e login do usuário, complete as seguintes etapas.

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até a**visão geral**da proteção geral da**proteção de proteção de** > **identidade** > do diretório > ativo do **Azure**.
1. Selecione **Configurar a política de risco do usuário**.
   1. Em **Atribuições**
      1. **Usuários** - Escolha **todos os usuários** ou **Selecione indivíduos e grupos** se limitar sua distribuição.
         1. Opcionalmente, você pode optar por excluir os usuários da apólice.
      1. **Condições** - **Risco do usuário** A recomendação da Microsoft é definir essa opção como **Alta**.
   1. **Controles**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir alteração de **senha.**
   1. **Impor política** - **sobre**
   1. **Salvar** - Esta ação o devolverá à página **Visão Geral.**
1. Selecione **Configurar a política de risco de login**.
   1. Em **Atribuições**
      1. **Usuários** - Escolha **todos os usuários** ou **Selecione indivíduos e grupos** se limitar sua distribuição.
         1. Opcionalmente, você pode optar por excluir os usuários da apólice.
      1. **Condições** - **Risco de login** A recomendação da Microsoft é definir essa opção como Média e **superior**.
   1. **Controles**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir **autenticação multifatorial.**
   1. **Impor política** - **sobre**
   1. **Salvar**

## <a name="next-steps"></a>Próximas etapas

- [Habilite a política de registro de autenticação multifatorial do Azure](howto-identity-protection-configure-mfa-policy.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Investigar detecções de risco](howto-identity-protection-investigate-risk.md)

- [Simular detecções de risco](howto-identity-protection-simulate-risk.md)
