---
title: Autenticação baseada em certificados Android - Azure Active Directory
description: Saiba mais sobre os cenários com suporte e os requisitos para configuração de autenticação baseada em certificado em soluções com dispositivos Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9f38c160a72b6c414cdde7d29e4056a4068cdc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848809"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação baseada em certificado do Azure Active Directory no Android

Os dispositivos Android podem usar CBA (Autenticação Baseada em Certificado) para autenticarem-se no Azure Active Directory usando um certificado do cliente nos dispositivos durante a conexão a:

* Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word
* Clientes do EAS (Exchange ActiveSync)

Configurar esse recurso elimina a necessidade de digitar uma combinação de nome de usuário e senha em determinados emails e aplicativos do Microsoft Office no seu dispositivo móvel.

Este tópico fornece os requisitos e os cenários com suporte para configurar a CBA em um dispositivo iOS(Android) para usuários de locatários nos planos do Office 365 Enterprise, Business, Education, US Government, China e Germany.

Esse recurso está disponível na visualização em planos do governo federal e para defesa governamental dos EUA do Office 365.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicativos móveis da Microsoft

| Aplicativos | Suporte |
| --- | --- |
| Aplicativo de Proteção de Informações do Azure |![Verifique a marca que significa suporte para este aplicativo][1] |
| Portal da Empresa do Intune |![Verifique a marca que significa suporte para este aplicativo][1] |
| Equipes da Microsoft |![Verifique a marca que significa suporte para este aplicativo][1] |
| OneNote |![Verifique a marca que significa suporte para este aplicativo][1] |
| OneDrive |![Verifique a marca que significa suporte para este aplicativo][1] |
| Outlook |![Verifique a marca que significa suporte para este aplicativo][1] |
| Power BI |![Verifique a marca que significa suporte para este aplicativo][1] |
| Skype for Business |![Verifique a marca que significa suporte para este aplicativo][1] |
| Word/Excel/PowerPoint |![Verifique a marca que significa suporte para este aplicativo][1] |
| Yammer |![Verifique a marca que significa suporte para este aplicativo][1] |

### <a name="implementation-requirements"></a>Requisitos de implementação

A versão do sistema operacional do dispositivo deve ser Android 5.0 (Lollipop) e superior.

Um servidor de federação deve ser configurado.

Para que o Azure Active Directory revogue um certificado do cliente, o token ADFS deve ter as seguintes declarações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (O número de série do certificado do cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (A cadeia de caracteres para o emissor do certificado do cliente)

O Azure Active Directory adiciona essas declarações ao token de atualização se elas estiverem disponíveis no token ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação.

Como melhor prática, é necessário atualizar as páginas de erro de ADFS da organização com as informações a seguir:

* O requisito para instalar o Microsoft Authenticator no Android.
* Instruções sobre como obter um certificado de usuário.

Para obter mais informações, consulte [Personalizando as páginas de entrada do AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Alguns aplicativos do Office (com autenticação moderna habilitada) enviam '*prompt=login*' ao Azure AD na solicitação. Por padrão, o Microsoft Azure AD converte ‘*prompt=login*’ na solicitação para ADFS como ‘*wauth=usernamepassworduri*’ (solicita que o ADFS faça uma autenticação U/P) e ‘*wfresh=0*’ (solicita que o ADFS ignore o estado de SSO e faça uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificado para esses aplicativos, precisará modificar o comportamento padrão do Azure AD. Defina ‘*PromptLoginBehavior*’ nas configurações do domínio federado como ‘*Disabled*‘.
Você pode usar o cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar essa tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Suporte aos clientes do Exchange ActiveSync

Há suporte para determinados aplicativos do Exchange ActiveSync no Android 5.0 (Lollipop) ou superior. Para determinar se o aplicativo de email dá suporte a esse recurso, contate o desenvolvedor do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar a autenticação baseada em certificado em seu ambiente, confira [Get started with certificate-based authentication on Android](active-directory-certificate-based-authentication-get-started.md) (Introdução à autenticação baseada em certificado no Android) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
