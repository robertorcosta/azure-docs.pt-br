---
title: Autenticação baseada em certificados no iOS - Azure Active Directory
description: Conheça os cenários suportados e os requisitos para configurar autenticação baseada em certificados para o Azure Active Directory em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639624"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticação baseada em certificado do Azure Active Directory no iOS

Para melhorar a segurança, os dispositivos iOS podem usar a autenticação baseada em certificados (CBA) para autenticar o Azure Active Directory (Azure AD) usando um certificado de cliente em seu dispositivo ao se conectar aos seguintes aplicativos ou serviços:

* Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word
* Clientes do EAS (Exchange ActiveSync)

O uso de certificados elimina a necessidade de inserir uma combinação de nome de usuário e senha em certos aplicativos de e-mail e Microsoft Office em seu dispositivo móvel.

Este artigo detalha os requisitos e os cenários suportados para configurar o CBA em um dispositivo iOS. A CBA para iOS está disponível em nuvens públicas do Azure, Microsoft Government Cloud, Microsoft Cloud Germany e Microsoft Azure China 21Vianet.

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

## <a name="requirements"></a>Requisitos

Para usar o CBA com o iOS, os seguintes requisitos e considerações se aplicam:

* A versão do sistema operacional do dispositivo deve ser iOS 9 ou superior.
* É necessário um Microsoft Authenticator para aplicativos do Office em iOS.
* Uma preferência de identidade deve ser criada no chaveiro macOS que inclua a URL de autenticação do servidor ADFS. Para obter mais informações, consulte [Criar uma preferência de identidade no Keychain Access no Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Os seguintes requisitos e considerações dos Serviços e considerações da Active Directory Federation Services (ADFS) aplicam-se:

* O servidor ADFS deve ser habilitado para autenticação de certificados e usar autenticação federada.
* O certificado precisa usar o Enhanced Key Use (EKU) e conter o UPN do usuário no *Nome Principal do Assunto (NOME PRINCIPAL NT)*.

## <a name="configure-adfs"></a>Configurar o ADFS

Para que o Azure AD revogue um certificado de cliente, o token ADFS deve ter as seguintes reivindicações. O Azure AD adiciona essas reivindicações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- adicione o número de série do seu certificado de cliente
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- adicionar a string para o emissor do seu certificado de cliente

Como uma prática recomendada, você também deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Autenticador Microsoft no iOS.
* Instruções sobre como obter um certificado de usuário.

Para obter mais informações, consulte [Personalizando o sinal de AD FS na página](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Use autenticação moderna com aplicativos do Office

Alguns aplicativos do Office com `prompt=login` autenticação moderna habilitadas enviam para o Azure AD em sua solicitação. Por padrão, o Azure `prompt=login` AD traduz na `wauth=usernamepassworduri` solicitação para a ADFS como (pede `wfresh=0` à ADFS para fazer U/P Auth) e (pede ao ADFS para ignorar o estado SSO e fazer uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificados para esses aplicativos, modifique o comportamento padrão do Azure AD.

Para atualizar o comportamento padrão, defina o '*PromptLoginBehavior*' em suas configurações de domínio federadas *como Desativado*. Você pode usar o [cmdlet MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar essa tarefa, conforme mostrado no exemplo a seguir:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Suporte para clientes Exchange ActiveSync

No iOS 9 ou posterior, há suporte para o cliente de email do iOS nativo. Para determinar se esse recurso é suportado para todos os outros aplicativos do Exchange ActiveSync, entre em contato com o desenvolvedor do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para configurar a autenticação baseada em certificados em seu ambiente, consulte [Iniciar com autenticação baseada em certificados](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
