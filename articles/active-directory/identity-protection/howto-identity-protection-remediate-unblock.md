---
title: Remediar riscos e desbloquear usuários no Azure AD Identity Protection
description: Conheça as opções que você tem dedeções de risco ativos próximas.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382111"
---
# <a name="remediate-risks-and-unblock-users"></a>Remediar riscos e desbloquear usuários

Depois de concluir sua [investigação,](howto-identity-protection-investigate-risk.md)você vai querer tomar medidas para remediar o risco ou desbloquear os usuários. As organizações também têm a opção de permitir remediação automatizada usando suas [políticas de risco.](howto-identity-protection-configure-risk-policies.md) As organizações devem tentar fechar todas as detecções de risco com as que são apresentadas em um período de tempo com o que sua organização está confortável. A Microsoft recomenda encerrar eventos o mais rápido possível porque o tempo importa ao trabalhar com risco.

## <a name="remediation"></a>Correção

Todas as detecções ativas de risco contribuem para o cálculo de um valor chamado nível de risco do usuário. O nível de risco do usuário é um indicador (baixo, médio, alto) para a probabilidade de que uma conta tenha sido comprometida. Como administrador, você deseja fechar todas as detecções de risco, para que os usuários afetados não estejam mais em risco.

Algumas detecções de riscos podem ser marcadas pela Proteção de Identidade como "Fechado (sistema)" porque os eventos não foram mais determinados como arriscados.

Os administradores têm as seguintes opções para remediar:

- Auto-remediação com política de risco
- Redefinição de senha manual
- Ignorar o risco de usuário
- Fechar detecções de risco individuais manualmente

### <a name="self-remediation-with-risk-policy"></a>Auto-remediação com política de risco

Se você permitir que os usuários se auto-remediam, com o MFA (Multi-Factor Authentication, autenticação multifatorial) do Azure e o reset de senha de autoatendimento (SSPR) em suas políticas de risco, eles podem se desbloquear quando o risco for detectado. Essas detecções são então consideradas fechadas. Os usuários devem ter se registrado previamente para OZure MFA e SSPR para usar quando o risco é detectado.

Algumas detecções podem não aumentar o risco para o nível em que uma auto-remediação do usuário seria necessária, mas os administradores ainda devem avaliar essas detecções. Os administradores podem determinar que medidas adicionais são necessárias, como bloquear o [acesso dos locais](../conditional-access/howto-conditional-access-policy-location.md) ou reduzir o risco aceitável em suas políticas.

### <a name="manual-password-reset"></a>Redefinição de senha manual

Se exigir uma redefinição de senha usando uma política de risco de usuário não for uma opção, os administradores podem fechar todas as detecções de risco para um usuário com uma redefinição manual de senha.

Os administradores recebem duas opções ao redefinir uma senha para seus usuários:

- **Gerar uma senha temporária** – ao gerar uma senha temporária, você pode imediatamente deixar uma identidade em um estado seguro novamente. Este método requer entrar em contato com os usuários afetados porque eles precisam saber qual é a senha temporária. Como a senha é temporária, o usuário é solicitado a alterar a senha para algo novo durante o próximo login.

- **Exigir que o usuário redefina a senha** – exigir que os usuários redefinam senhas permite a autorrecuperação sem entrar em contato com o suporte técnico nem com o administrador. Este método só se aplica aos usuários registrados para OZure MFA e SSPR. Para usuários que não foram cadastrados, essa opção não está disponível.

### <a name="dismiss-user-risk"></a>Ignorar o risco de usuário

Se uma redefinição de senha não for uma opção para você, porque, por exemplo, o usuário foi excluído, você pode optar por descartar detecções de risco do usuário.

Quando você **clica em Descartar o risco do usuário,** todos os eventos estão fechados e o usuário afetado não está mais em risco. No entanto, como esse método não tem um impacto sobre a senha existente, ele não deixa a identidade relacionada em um estado seguro novamente. 

### <a name="close-individual-risk-detections-manually"></a>Fechar detecções de risco individuais manualmente

Você pode fechar detecções de risco individuais manualmente. Ao fechar detecções de risco manualmente, você pode reduzir o nível de risco do usuário. Normalmente, as detecções de risco são fechadas manualmente em resposta a uma investigação relacionada. Por exemplo, ao falar com um usuário revela que uma detecção ativa de risco não é mais necessária. 
 
Ao fechar detecções de risco manualmente, você pode optar por tomar qualquer uma das seguintes ações para alterar o status de uma detecção de risco:

- Confirmar usuário comprometido
- Ignorar o risco de usuário
- Confirme o cofre de login
- Confirme o login comprometido

## <a name="unblocking-users"></a>Desbloquear usuários

Um administrador pode optar por bloquear um login com base em sua política de risco ou investigações. Um bloqueio pode ocorrer com base no login ou no risco do usuário.

### <a name="unblocking-based-on-user-risk"></a>Desbloqueio com base no risco do usuário

Para desbloquear uma conta bloqueada devido ao risco do usuário, os administradores têm as seguintes opções:

1. **Redefinir senha** - você pode redefinir a senha do usuário.
1. **Descarte o risco do usuário** - A política de risco do usuário bloqueia um usuário se o nível de risco do usuário configurado para bloquear o acesso foi atingido. Você pode reduzir o nível de risco de um usuário descartando o risco do usuário ou fechando manualmente as detecções de risco relatadas.
1. **Exclua o usuário da política** - Se você acha que a configuração atual da sua política de login está causando problemas para usuários específicos, você pode excluir os usuários dela. Para obter mais informações, consulte a seção Exclusões no artigo [Como: Configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Para obter mais informações, consulte o artigo [Como: Configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Desbloqueio com base no risco de login

Para desbloquear uma conta com base no risco de login, os administradores têm as seguintes opções:

1. **Entrada de um local ou dispositivo familiar** – uma razão comum para entradas suspeitas bloqueadas são as tentativas de entrada de locais ou de dispositivos desconhecidos. Seus usuários podem determinar rapidamente se essa razão é a razão de bloqueio ao tentar fazer login de um local ou dispositivo familiar.
1. **Exclua o usuário da política** - Se você acha que a configuração atual da sua política de login está causando problemas para usuários específicos, você pode excluir os usuários dela. Para obter mais informações, consulte a seção Exclusões no artigo [Como: Configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Desabilitar política** - se você achar que sua configuração de política está causando problemas para todos os usuários, poderá desabilitar a política. Para obter mais informações, consulte o artigo [Como: Configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview-identity-protection.md).
