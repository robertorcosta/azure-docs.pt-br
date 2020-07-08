---
title: Configurar a política de registro de MFA-Azure Active Directory Identity Protection
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84464327"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como configurar a política de registro da autenticação multifator do Azure

Azure AD Identity Protection ajuda a gerenciar a distribuição do registro de autenticação multifator do Azure (MFA) configurando uma política de acesso condicional para exigir o registro de MFA, independentemente do aplicativo de autenticação moderno no qual você está entrando.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registro da autenticação multifator do Azure?

A autenticação multifator do Azure fornece um meio de verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para entradas de usuário. Para que os usuários possam responder aos prompts do MFA, eles devem primeiro se registrar para a autenticação multifator do Azure.

Recomendamos que você exija a autenticação multifator do Azure para entradas de usuário porque:

- Fornece autenticação forte por meio de uma variedade de opções de verificação.
- Desempenha um papel fundamental na preparação de sua organização para a remediação automática de detecções de risco no Identity Protection.

Para obter mais informações sobre a autenticação multifator do Azure, consulte [o que é a autenticação multifator do Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuração de política

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **Security**  >  política de registro do MFA da**proteção de identidade**de segurança  >  **MFA registration policy**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
   1. Em **controles**
      1. Certifique-se de que a caixa de seleção **exigir registro de MFA do Azure** esteja marcada e escolha **selecionar**.
   1. **Impor política**  -  **Em**
   1. **Salvar**

## <a name="user-experience"></a>Experiência do usuário

Azure Active Directory Identity Protection solicitará que os usuários se registrem na próxima vez que entrarem de forma interativa e terão 14 dias para concluir o registro. Durante esse período de 14 dias, eles podem ignorar o registro, mas no final do período eles deverão ser registrados para que possam concluir o processo de entrada.

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Experiências de entrada com o Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Próximas etapas

- [Habilitar as políticas de risco com usuários e entradas](howto-identity-protection-configure-risk-policies.md)

- [Habilitar a redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a Autenticação Multifator do Microsoft Azure](../authentication/howto-mfa-getstarted.md)
