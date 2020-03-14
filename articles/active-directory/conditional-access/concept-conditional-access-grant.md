---
title: Conceder controles na política de acesso condicional-Azure Active Directory
description: O que são controles Grant em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0716c2d4475bb538c06b9a591521fbdcfc0c80e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263433"
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
- [Exigir que o dispositivo seja marcado como em conformidade (Microsoft Intune)](/intune/protect/device-compliance-get-started)
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

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade específicos. Essas informações de conformidade de política são encaminhadas do Intune para o Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos. Para obter mais informações sobre políticas de conformidade, consulte o artigo [definir regras em dispositivos para permitir o acesso a recursos em sua organização usando o Intune](/intune/protect/device-compliance-get-started).

Um dispositivo pode ser marcado como compatível pelo Intune (para qualquer sistema operacional do dispositivo) ou por um sistema MDM de terceiros para dispositivos Windows 10. O JAMF pro é o único sistema MDM de terceiros com suporte. Mais informações sobre a integração podem ser encontradas no artigo [integrar o JAMF pro ao Intune para fins de conformidade](/intune/protect/conditional-access-integrate-jamf).

Os dispositivos devem ser registrados no Azure AD antes que possam ser marcados como em conformidade. Mais informações sobre o registro de dispositivo podem ser encontradas no artigo [o que é uma identidade de dispositivo](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Exigir dispositivo ingressado no Azure AD híbrido

As organizações podem optar por usar a identidade do dispositivo como parte de sua política de acesso condicional. As organizações podem exigir que os dispositivos sejam ingressados no Azure AD híbrido usando essa caixa de seleção. Para obter mais informações sobre identidades de dispositivo, consulte o artigo [o que é uma identidade de dispositivo?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Exigir um aplicativo cliente aprovado

As organizações podem exigir que uma tentativa de acesso aos aplicativos de nuvem selecionados precise ser feita de um aplicativo cliente aprovado. Esses aplicativos cliente aprovados dão suporte a [políticas de proteção de aplicativo do Intune](/intune/app-protection-policy) independentemente de qualquer solução de MDM (gerenciamento de dispositivo móvel).

Essa configuração se aplica aos seguintes aplicativos iOS e Android:

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
- Microsoft Office
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

Consulte o artigo [como: exigir aplicativos cliente aprovados para acesso de aplicativo de nuvem com acesso condicional](app-based-conditional-access.md) para exemplos de configuração.

### <a name="require-app-protection-policy"></a>Requer política de proteção do aplicativo

Em sua política de acesso condicional, você pode exigir que uma [política de proteção de aplicativo do Intune](/intune/app-protection-policy) esteja presente no aplicativo cliente antes que o acesso esteja disponível para os aplicativos de nuvem selecionados. 

Essa configuração se aplica aos seguintes aplicativos cliente:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Comentários**

- Aplicativos para a política de proteção de aplicativo dão suporte ao recurso de gerenciamento de aplicativos móveis do Intune com proteção de política.
- Os requisitos da **política exigir proteção de aplicativo** :
    - O só dá suporte ao iOS e ao Android para a condição de plataforma de dispositivo.

Consulte o artigo [como: exigir a política de proteção de aplicativo e um aplicativo cliente aprovado para acesso de aplicativo de nuvem com acesso condicional](app-protection-based-conditional-access.md) para exemplos de configuração.

### <a name="terms-of-use"></a>Termos de uso

Se sua organização tiver criado termos de uso, opções adicionais poderão estar visíveis em conceder controles. Essas opções permitem que os administradores exijam a confirmação dos termos de uso como uma condição de acessar os recursos protegidos pela política. Mais informações sobre os termos de uso podem ser encontradas no artigo [Azure Active Directory termos de uso](terms-of-use.md).

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: controles de sessão](concept-conditional-access-session.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo somente de relatório](concept-conditional-access-report-only.md)
