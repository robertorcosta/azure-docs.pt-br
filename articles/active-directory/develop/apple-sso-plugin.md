---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in de SSO Azure Active Directory da Microsoft para dispositivos iOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982575"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in do SSO corporativo da Microsoft para dispositivos Apple (versão prévia)

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece logon único (SSO) para contas do Azure Active Directory (AD do Azure) em todos os aplicativos que dão suporte ao recurso de [logon único corporativo](https://developer.apple.com/documentation/authenticationservices) da Apple. A Microsoft trabalhou junto com a Apple para desenvolver esse plug-in a fim de aumentar a usabilidade do seu aplicativo e, ao mesmo tempo, fornecer a melhor proteção que a Apple e a Microsoft podem fornecer.

Nesta versão de visualização pública, o plug-in de SSO corporativo está disponível apenas para dispositivos iOS e é distribuído em determinados aplicativos da Microsoft.

Nosso primeiro uso do plug-in de SSO corporativo é com nosso novo recurso de [modo de dispositivo compartilhado](msal-ios-shared-devices.md) .

## <a name="features"></a>Recursos

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece SSO para contas do Azure AD em todos os aplicativos que dão suporte ao recurso de logon único corporativo da Apple.
- Entregue automaticamente no Microsoft Authenticator e pode ser habilitado por qualquer solução de MDM (gerenciamento de dispositivo móvel).

## <a name="requirements"></a>Requisitos

Para usar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- o iOS 13,0 ou superior deve estar instalado no dispositivo.
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos incluem o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- O dispositivo deve ser registrado no MDM (por exemplo, com Microsoft Intune).
- A configuração deve ser enviada por push para o dispositivo para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple no dispositivo. Essa restrição de segurança é exigida pela Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Habilitar a extensão SSO com o MDM (gerenciamento de dispositivo móvel)

Para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple, seus dispositivos precisam receber um sinal por meio de um serviço MDM. Como a Microsoft inclui o plug-in de SSO corporativo no [aplicativo Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), use o MDM para configurar o aplicativo para habilitar o plug-in Microsoft Enterprise SSO.

Use os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- **Tipo**: redirecionar
- **ID da extensão**:`com.microsoft.azureauthenticator.ssoextension`
- **ID da equipe**:`SGGM6D27TK`
- **URLs**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Você pode usar Microsoft Intune como seu serviço de MDM para facilitar a configuração do plug-in do SSO corporativo da Microsoft. Para obter mais informações, consulte a [documentação de configuração do Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Usando a extensão de SSO em seu aplicativo

A [MSAL (biblioteca de autenticação da Microsoft) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple.

Se você quiser dar suporte ao modo de dispositivo compartilhado fornecido pelo plug-in Microsoft Enterprise SSO para dispositivos Apple, verifique se seus aplicativos usam a versão mínima necessária especificada do MSAL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o modo de dispositivo compartilhado no iOS, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md).
