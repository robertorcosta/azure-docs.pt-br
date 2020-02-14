---
title: Conceder controles na política de acesso condicional-Azure Active Directory
description: O que são controles Grant em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192110"
---
# <a name="conditional-access-grant"></a>Acesso condicional: Grant

Em uma política de acesso condicional, um administrador pode fazer uso de controles de acesso para conceder ou bloquear o acesso a recursos.

![Política de acesso condicional com um controle Grant que exige autenticação multifator](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Acesso bloqueado

O bloco leva em conta quaisquer atribuições e impede o acesso com base na configuração da política de acesso condicional.

O bloco é um controle poderoso que deve ser atraente com o conhecimento apropriado. É algo que os administradores devem usar o [modo somente de relatório](concept-conditional-access-report-only.md) para testar antes de habilitar.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controles ao conceder acesso. Esses controles incluem as seguintes opções: 

- [Exigir autenticação multifator (autenticação multifator do Azure)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como em conformidade (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Exigir dispositivo ingressado no Azure AD híbrido](../devices/concept-azure-ad-join-hybrid.md)
- [Exigir aplicativo cliente aprovado](app-based-conditional-access.md)
- [Exigir política de proteção de aplicativo](app-protection-based-conditional-access.md)

Quando os administradores optam por combinar essas opções, eles podem escolher os seguintes métodos:

- Exigir todos os controles selecionados (controle **e** controle)
- Exigir um dos controles selecionados (controle **ou** controle)

Por padrão, o acesso condicional requer todos os controles selecionados.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

A seleção dessa caixa de seleção exigirá que os usuários executem a autenticação multifator do Azure. Mais informações sobre a implantação da autenticação multifator do Azure podem ser encontradas no artigo [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade específicos. Essas informações de conformidade de política são encaminhadas do Intune para o Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos. Para obter mais informações sobre políticas de conformidade, consulte o artigo [definir regras em dispositivos para permitir o acesso a recursos em sua organização usando o Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

### <a name="require-hybrid-azure-ad-joined-device"></a>Exigir dispositivo ingressado no Azure AD híbrido

As organizações podem optar por usar a identidade do dispositivo como parte de sua política de acesso condicional. As organizações podem exigir que os dispositivos sejam ingressados no Azure AD híbrido usando essa caixa de seleção. Para obter mais informações sobre identidades de dispositivo, consulte o artigo [o que é uma identidade de dispositivo?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Exigir um aplicativo cliente aprovado

As organizações podem exigir que uma tentativa de acesso aos aplicativos de nuvem selecionados precise ser feita de um aplicativo cliente aprovado.

Essa configuração se aplica aos seguintes aplicativos cliente:

- Proteção de Informações do Microsoft Azure
- Livros da Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Comentários**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.
- O requisito **Exigir o aplicativo do cliente aprovado**:
   - O só dá suporte ao iOS e ao Android para a condição de plataforma de dispositivo.
- O acesso condicional não pode considerar o Microsoft Edge no modo InPrivate em um aplicativo cliente aprovado.

### <a name="require-app-protection-policy"></a>Requer política de proteção do aplicativo

Em sua política de acesso condicional, você pode exigir que uma política de proteção de aplicativo esteja presente no aplicativo cliente antes que o acesso esteja disponível para os aplicativos de nuvem selecionados. 

![Controlar o acesso com a política de proteção de aplicativo](./media/technical-reference/22.png)

Essa configuração se aplica aos seguintes aplicativos cliente:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Comentários**

- Aplicativos para a política de proteção de aplicativo dão suporte ao recurso de gerenciamento de aplicativos móveis do Intune com proteção de política.
- Os requisitos da **política exigir proteção de aplicativo** :
    - O só dá suporte ao iOS e ao Android para a condição de plataforma de dispositivo.

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: controles de sessão](concept-conditional-access-session.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo somente de relatório](concept-conditional-access-report-only.md)
