---
title: Plug-in Microsoft Enterprise SSO para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in SSO do Azure Active Directory da Microsoft para dispositivos iOS e macOS.
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
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550312"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO para dispositivos Apple (Preview)

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece o sso de login único (SSO) para contas de Diretório Ativo em todos os aplicativos que suportam o recurso Enterprise Single [Sign-On](https://developer.apple.com/documentation/authenticationservices) da Apple. A Microsoft trabalhou em estreita colaboração com a Apple para desenvolver esse plug-in para aumentar a usabilidade do seu aplicativo, ao mesmo tempo em que oferece a melhor proteção que a Apple e a Microsoft podem fornecer.

Nesta versão de Visualização Pública, o plug-in Enterprise SSO está disponível apenas para dispositivos iOS e é distribuído em certos aplicativos da Microsoft.

Nosso primeiro uso do plug-in Enterprise SSO é com nosso novo recurso [de modo de dispositivo compartilhado.](msal-ios-shared-devices.md)

## <a name="features"></a>Recursos

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece sso para contas de diretório ativo em todos os aplicativos que suportam o recurso enterprise single sign-on da Apple.
- Entregue automaticamente no Microsoft Authenticator e pode ser habilitado por qualquer solução de MDM (Mobile Device Management, gerenciamento de dispositivos móveis).

## <a name="requirements"></a>Requisitos

Para usar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O iOS 13.0 ou superior deve ser instalado no dispositivo.
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para visualização pública, esses aplicativos incluem o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- O dispositivo deve ser inscrito em MDM (por exemplo, com o Microsoft Intune).
- A configuração deve ser empurrada para o dispositivo para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple no dispositivo. Essa restrição de segurança é exigida pela Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Habilite a extensão SSO com gerenciamento de dispositivos móveis (MDM)

Para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple, seus dispositivos precisam ser enviados um sinal através de um serviço MDM. Como a Microsoft inclui o plug-in Enterprise SSO no [aplicativo Microsoft Authenticator,](..//user-help/user-help-auth-app-overview.md)use seu MDM para configurar o aplicativo para habilitar o plug-in Microsoft Enterprise SSO.

Use os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- **Tipo:** Redirecionamento
- **ID de extensão**:`com.microsoft.azureauthenticator.ssoextension`
- **ID da equipe**:`SGGM6D27TK`
- **URLs:**
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

Você pode usar o Microsoft Intune como seu serviço MDM para facilitar a configuração do plug-in Microsoft Enterprise SSO. Para obter mais informações, consulte a documentação de configuração do [Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Usando a extensão SSO em seu aplicativo

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple.

Se você quiser oferecer suporte ao modo de dispositivo compartilhado fornecido pelo plug-in Microsoft Enterprise SSO para dispositivos Apple, certifique-se de que seus aplicativos usem a versão mínima exigida do MSAL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o modo dispositivo compartilhado no iOS, consulte [modo de dispositivo compartilhado para dispositivos iOS](msal-ios-shared-devices.md).
