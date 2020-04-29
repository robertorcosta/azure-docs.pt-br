---
title: Acesso condicional-acesso condicional com base em risco-Azure Active Directory
description: Criar políticas de acesso condicional para habilitar aprimoramentos de proteção de identidade para políticas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295146"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Acesso condicional: acesso condicional com base em risco

Organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporam Azure AD Identity Protection detecções de risco. Há três políticas padrão que podem ser habilitadas prontamente. 

* Exigir que todos os usuários se registrem para a autenticação multifator do Azure.
* Exigir uma alteração de senha para usuários que são de alto risco.
* Exigir autenticação multifator para usuários com risco de entrada médio ou alto.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Exigir que todos os usuários se registrem para a autenticação multifator do Azure

Habilitar essa política exigirá que todos os usuários se registrem para a autenticação multifator do Azure dentro de 14 dias. 

1. Entre no **portal do Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique em **Registro de MFA**.
1. Em **atribuições**, selecione **usuários**.
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**. 
   1. Selecione **Concluído**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Salvar**.

## <a name="require-a-password-change-high-risk-users"></a>Exigir uma alteração de senha de usuários de alto risco

A Microsoft trabalha com pesquisadores, autoridades, várias equipes de segurança da Microsoft e outras fontes confiáveis para localizar os pares de nome de usuário e senha. Quando um desses pares corresponde a uma conta em seu ambiente, uma alteração de senha com base em risco pode ser disparada usando a política a seguir.

1. Entre no **portal do Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique em **política de risco do usuário**.
1. Em **atribuições**, selecione **usuários**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Concluído**.
1. Em **condições**, selecione **risco do usuário**e, em seguida, escolha **alta**.
   1. Clique em **selecionar** e em **concluído**.
1. Em **controles** > de**acesso**, escolha **permitir acesso**e, em seguida, selecione **exigir alteração de senha**.
   1. Clique em **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Salvar**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Exigir usuários de risco de entrada média ou alta MFA

A maioria dos usuários tem um comportamento normal que pode ser acompanhado; quando eles saem do padrão, pode ser arriscado permitir que eles se conectem diretamente. Talvez você queira bloquear esse usuário ou talvez apenas pedir que eles executem a autenticação multifator para provar que eles são realmente quem dizem que estão. Para habilitar uma política exigindo MFA quando uma entrada arriscada for detectada, habilite a política a seguir.

1. Entre no **portal do Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique na **política de risco de entrada**
1. Em **atribuições**, selecione **usuários**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Concluído**.
1. Em **condições**, selecione **risco de entrada**e, em seguida, escolha **médio e superior**.
   1. Clique em **selecionar** e em **concluído**.
1. Em **controles** > de**acesso**, escolha **permitir acesso**e, em seguida, selecione **exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)

[Como funciona: autenticação multifator do Azure](../authentication/concept-mfa-howitworks.md)

[O que é Azure Active Directory Identity Protection?](../identity-protection/overview.md)
