---
title: Configure a política de registro Do MFA - Azure Active Directory Identity Protection
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382138"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registro de autenticação multifatorial do Azure

O Azure AD Identity Protection ajuda você a gerenciar o lançamento do registro de Autenticação Multifatorial (MFA) do Azure, configurando uma política de Acesso Condicional para exigir o registro do MFA, não importa em qual aplicativo de autenticação moderno você está fazendo login.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registro de autenticação multifatorial do Azure?

A Autenticação Multifatorial do Azure fornece um meio para verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para logins de usuário. Para que os usuários possam responder às solicitações do MFA, eles devem primeiro se registrar para a Autenticação Multifatorial do Azure.

Recomendamos que você exija a autenticação multifatorial do Azure para fazer logins de usuário porque:

- Oferece autenticação forte através de uma gama de opções de verificação.
- Desempenha um papel fundamental na preparação de sua organização para se auto-remediar a partir de detecções de risco na Proteção de Identidade.

Para obter mais informações sobre a autenticação multifatorial do Azure, consulte [o que é autenticação multifatorial do Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuração de política

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até a política de**registro MFA****de proteção** > de**identidade** > do diretório ativo do >  **Azure**.
   1. Em **Atribuições**
      1. **Usuários** - Escolha **todos os usuários** ou **Selecione indivíduos e grupos** se limitar sua distribuição.
         1. Opcionalmente, você pode optar por excluir os usuários da apólice.
   1. **Controles**
      1. Certifique-se de que a caixa de seleção **exija que o registro do Azure MFA** seja verificado e escolha **Selecionar**.
   1. **Impor política** - **sobre**
   1. **Salvar**

## <a name="user-experience"></a>Experiência do usuário

O Azure Active Directory Identity Protection solicitará que seus usuários se registrem na próxima vez que fizerem login de forma interativa e terão 14 dias para concluir o registro. Durante esse período de 14 dias, eles podem contornar o registro, mas ao final do período serão obrigados a se registrar antes de completar o processo de login.

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Experiências de entrada com o Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Próximas etapas

- [Habilite políticas de login e risco de usuário](howto-identity-protection-configure-risk-policies.md)

- [Habilitar a redefinição da senha de autoatendimento do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a Autenticação Multifator do Microsoft Azure](../authentication/howto-mfa-getstarted.md)
