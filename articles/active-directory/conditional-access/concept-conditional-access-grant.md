---
title: Controles de subvenção na política de acesso condicional - Diretório Ativo do Azure
description: Quais são os controles de subvenção em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331849"
---
# <a name="conditional-access-grant"></a>Acesso Condicional: Subvenção

Dentro de uma política de acesso condicional, um administrador pode fazer uso de controles de acesso para conceder ou bloquear o acesso aos recursos.

![Política de Acesso Condicional com controle de subvenção que exija autenticação multifatorial](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Acesso bloqueado

O bloco leva em conta quaisquer atribuições e impede o acesso com base na configuração da diretiva Acesso Condicional.

O bloco é um controle poderoso que deve ser exercido com conhecimento apropriado. É algo que os administradores devem usar [o modo somente relatório](concept-conditional-access-report-only.md) para testar antes de habilitar.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controles ao conceder acesso. Esses controles incluem as seguintes opções: 

- [Exigir autenticação multifatorial (Autenticação Multifatorial do Azure)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como compatível (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Exigir dispositivo adepto a adesão a azure azure híbrido](../devices/concept-azure-ad-join-hybrid.md)
- [Exigir um aplicativo cliente aprovado](app-based-conditional-access.md)
- [Requer política de proteção do aplicativo](app-protection-based-conditional-access.md)

Quando os administradores optam por combinar essas opções, eles podem escolher os seguintes métodos:

- Requerem todos os controles selecionados (controle **E** controle)
- Requerem um dos controles selecionados (controle **OU** controle)

Por padrão, o Acesso Condicional requer todos os controles selecionados.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

A seleção desta caixa de seleção exigirá que os usuários realizem a autenticação multifatorial do Azure. Mais informações sobre a implantação da Autenticação Multifatorial do Azure podem ser encontradas no artigo [Planejamento de uma implantação de autenticação multifatorial baseada em nuvem](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

As organizações que implantaram o Microsoft Intune podem usar as informações devolvidas de seus dispositivos para identificar dispositivos que atendam a requisitos específicos de conformidade. Essas informações de conformidade de políticas são encaminhadas do Intune para o Azure AD, onde o Conditional Access pode tomar decisões para conceder ou bloquear o acesso aos recursos. Para obter mais informações sobre políticas de conformidade, consulte o artigo [Definir regras sobre dispositivos para permitir o acesso a recursos em sua organização usando o Intune](/intune/protect/device-compliance-get-started).

Um dispositivo pode ser marcado como compatível com o Intune (para qualquer sistema operacional de qualquer dispositivo) ou pelo sistema MDM de terceiros para dispositivos Windows 10. Jamf pro é o único sistema de MDM de terceiros suportado. Mais informações sobre integração podem ser encontradas no artigo, [Integrar Jamf Pro com intune para conformidade](/intune/protect/conditional-access-integrate-jamf).

Os dispositivos devem ser registrados no Azure AD antes de serem marcados como compatíveis. Mais informações sobre o registro do dispositivo podem ser encontradas no artigo, [O que é uma identidade de dispositivo.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Exigir dispositivo adepto a adesão a azure azure híbrido

As organizações podem optar por usar a identidade do dispositivo como parte de sua política de Acesso Condicional. As organizações podem exigir que os dispositivos sejam ads ad híbridos do Azure, que se juntam usando esta caixa de seleção. Para obter mais informações sobre as identidades dos dispositivos, consulte o artigo [O que é uma identidade de dispositivo?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Exigir um aplicativo cliente aprovado

As organizações podem exigir que uma tentativa de acesso aos aplicativos de nuvem selecionados precise ser feita a partir de um aplicativo cliente aprovado. Esses aplicativos clientes aprovados suportam políticas de proteção de [aplicativos Intune](/intune/app-protection-policy) independentes de qualquer solução de gerenciamento de dispositivos móveis (MDM).

Para aproveitar esse controle de concessão, o Conditional Access exige que o dispositivo seja registrado no Azure Active Directory, que requer o uso de um aplicativo de corretor. O aplicativo agente pode ser o Microsoft Authenticator para iOS ou o portal da Empresa da Microsoft para dispositivos Android. Se um aplicativo de corretora não estiver instalado no dispositivo quando o usuário tentar autenticar, o usuário será redirecionado para a loja de aplicativos para instalar o aplicativo corretor.

Esta configuração se aplica aos seguintes aplicativos para iOS e Android:

- Proteção de Informações do Microsoft Azure
- Microsoft Bookings
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
- Microsoft Office Hub
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
- Microsoft Whiteboard

**Observações**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.
- O requisito **Exigir o aplicativo do cliente aprovado**:
   - Fornece suporte apenas para iOS e Android para condição de plataforma de dispositivo.
   - Um aplicativo de corretor é necessário para registrar o dispositivo. No iOS, o aplicativo corretor é o Microsoft Authenticator e no Android, é o aplicativo Portal da Empresa Intune.
- O Conditional Access não pode considerar o Microsoft Edge no modo InPrivate um aplicativo cliente aprovado.

Veja o [artigo: Como: Exigir aplicativos clientes aprovados para acesso a aplicativos em nuvem com Acesso Condicional](app-based-conditional-access.md) para exemplos de configuração.

### <a name="require-app-protection-policy"></a>Requer política de proteção do aplicativo

Em sua política de acesso condicional, você pode exigir que uma [política de proteção de aplicativos Intune](/intune/app-protection-policy) esteja presente no aplicativo do cliente antes que o acesso esteja disponível para os aplicativos em nuvem selecionados. 

Para aproveitar esse controle de concessão, o Conditional Access exige que o dispositivo seja registrado no Azure Active Directory, que requer o uso de um aplicativo de corretor. O aplicativo agente pode ser o Microsoft Authenticator para iOS ou o portal da Empresa da Microsoft para dispositivos Android. Se um aplicativo de corretora não estiver instalado no dispositivo quando o usuário tentar autenticar, o usuário será redirecionado para a loja de aplicativos para instalar o aplicativo corretor.

Essa configuração se aplica aos seguintes aplicativos cliente:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Observações**

- Os aplicativos para a política de proteção de aplicativos suportam o recurso de gerenciamento de aplicativos móveis Intune com proteção de políticas.
- Os requisitos **da política de proteção de aplicativos Exigir:**
    - Fornece suporte apenas para iOS e Android para condição de plataforma de dispositivo.
    - Um aplicativo de corretor é necessário para registrar o dispositivo. No iOS, o aplicativo corretor é o Microsoft Authenticator e no Android, é o aplicativo Portal da Empresa Intune.

Veja o [artigo: Como: Exigir política de proteção de aplicativos e um aplicativo cliente aprovado para acesso a aplicativos em nuvem com Acesso Condicional](app-protection-based-conditional-access.md) para exemplos de configuração.

### <a name="terms-of-use"></a>Termos de uso

Se sua organização criou termos de uso, opções adicionais podem ser visíveis controles de subvenção. Essas opções permitem que os administradores exijam o reconhecimento dos termos de uso como condição para acessar os recursos protegidos pela política. Mais informações sobre os termos de uso podem ser encontradas no artigo, [termos de uso do Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: Controles de sessão](concept-conditional-access-session.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

- [Modo somente relatório](concept-conditional-access-report-only.md)
