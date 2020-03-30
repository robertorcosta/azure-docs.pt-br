---
title: Acesso Condicional - Acesso Condicional baseado em risco - Diretório Ativo do Azure
description: Crie políticas de acesso condicional para permitir melhorias na proteção de identidade para políticas
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295146"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Acesso Condicional: Acesso Condicional baseado em risco

As organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporem detecções de risco de proteção de identidade Azure AD. Existem três políticas padrão que podem ser habilitadas fora da caixa. 

* Exija que todos os usuários se registrem para autenticação multifatorial do Azure.
* Exija uma mudança de senha para usuários de alto risco.
* Exija autenticação multifatorial para usuários com risco médio ou alto de login.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Exija que todos os usuários se registrem para autenticação multifatorial do Azure

A habilitação desta política exigirá que todos os usuários se registrem para a Autenticação Multifatorial do Azure dentro de 14 dias. 

1. Faça login no **portal Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique em **Registro de MFA**.
1. Em **Atribuições,** selecione **Usuários**.
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** **selecione Selecionar usuários excluídos,** escolha as contas de acesso de emergência ou quebra-vidro da organização e **selecione Selecionar**. 
   1. Selecione **Feito**.
1. Definir **política de execução** para **on**.
1. Clique em **Salvar**.

## <a name="require-a-password-change-high-risk-users"></a>Exigir uma mudança de senha usuários de alto risco

A Microsoft trabalha com pesquisadores, autoridades, várias equipes de segurança da Microsoft e outras fontes confiáveis para localizar os pares de nome de usuário e senha. Quando um desses pares corresponde a uma conta em seu ambiente, uma alteração de senha com base em risco pode ser disparada usando a política a seguir.

1. Faça login no **portal Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique na **política de risco do usuário**.
1. Em **Atribuições,** selecione **Usuários**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** **selecione Selecionar usuários excluídos,** escolha as contas de acesso de emergência ou quebra-vidro da organização e **selecione Selecionar**.
   1. Selecione **Feito**.
1. Em **Condições,** selecione **Risco de Usuário**e escolha **Alto**.
   1. Clique **em Selecionar** e depois **feito**.
1. Em **Controls** > **Access,** escolha Permitir **acesso**e, em seguida, **selecione Exigir alteração de senha**.
   1. Clique em **Selecionar**.
1. Definir **política de execução** para **on**.
1. Clique em **Salvar**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Requerem usuários de risco médio ou alto de login mfa

A maioria dos usuários tem um comportamento normal que pode ser acompanhado; quando eles saem do padrão, pode ser arriscado permitir que eles se conectem diretamente. Você pode querer bloquear esse usuário ou talvez apenas pedir-lhes para realizar a autenticação multifatorial para provar que eles são realmente quem eles dizem que são. Para habilitar uma política exigindo MFA quando uma entrada arriscada for detectada, habilite a política a seguir.

1. Faça login no **portal Azure**.
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique na **política de risco de login**
1. Em **Atribuições,** selecione **Usuários**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** **selecione Selecionar usuários excluídos,** escolha as contas de acesso de emergência ou quebra-vidro da organização e **selecione Selecionar**.
   1. Selecione **Feito**.
1. Em **Condições,** selecione **Risco de entrada**e escolha Médio e **superior**.
   1. Clique **em Selecionar** e depois **feito**.
1. Em **Controls** > **Access,** escolha Permitir **acesso**e, em seguida, **selecione Exigir autenticação multifatorial**.
   1. Clique em **Selecionar**.
1. Definir **política de execução** para **on**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)

[Como funciona: autenticação multifator do Azure](../authentication/concept-mfa-howitworks.md)

[O que é Azure Active Directory Identity Protection?](../identity-protection/overview.md)
