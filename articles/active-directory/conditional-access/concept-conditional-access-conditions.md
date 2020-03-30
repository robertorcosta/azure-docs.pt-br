---
title: Condições na política de acesso condicional - Diretório Ativo do Azure
description: Quais são as condições em uma política de acesso condicional do Azure AD
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
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295343"
---
# <a name="conditional-access-conditions"></a>Acesso Condicional: Condições

Dentro de uma política de acesso condicional, um administrador pode fazer uso de sinais de condições como risco, plataforma de dispositivo ou localização para melhorar suas decisões políticas. 

![Defina uma política de acesso condicional e especifique condições](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Várias condições podem ser combinadas para criar políticas de acesso condicional de grãofino e específicos.

Por exemplo, ao acessar um aplicativo sensível, um administrador pode fatorar as informações de risco de login da Proteção de Identidade e a localização em sua decisão de acesso, além de outros controles, como autenticação multifatorial.

## <a name="sign-in-risk"></a>Risco de entrada

Para clientes com acesso à Proteção de [Identidade,](../identity-protection/overview-identity-protection.md)o risco de login pode ser avaliado como parte de uma política de Acesso Condicional. O risco de login representa a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade. Mais informações sobre o risco de login podem ser encontradas nos artigos, [o que é risco](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e [como: Configurar e habilitar políticas](../identity-protection/howto-identity-protection-configure-risk-policies.md)de risco .

## <a name="device-platforms"></a>Plataformas de dispositivo

A plataforma do dispositivo é caracterizada pelo sistema operacional que funciona em um dispositivo. O Azure AD identifica a plataforma usando informações fornecidas pelo dispositivo, como strings de agente de usuário. Uma vez que as strings do agente de usuário podem ser modificadas, essas informações não são verificadas. A plataforma do dispositivo deve ser usada em conjunto com as políticas de conformidade de dispositivos Microsoft Intune ou como parte de uma declaração de bloco. O padrão é aplicar em todas as plataformas de dispositivos.

O Azure AD Conditional Access suporta as seguintes plataformas de dispositivos:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Se você bloquear a autenticação legado usando a condição **Outros clientes,** você também poderá definir a condição da plataforma do dispositivo.

## <a name="locations"></a>Locais

Ao configurar o local como uma condição, as organizações podem optar por incluir ou excluir locais. Esses locais nomeados podem incluir as informações públicas da rede IPv4, país ou região, ou mesmo áreas desconhecidas que não mapeiam para países ou regiões específicas. Apenas os intervalos IP podem ser marcados como um local confiável.

Ao incluir **qualquer local,** essa opção inclui qualquer endereço IP na internet, não apenas locais nomeados configurados. Ao selecionar **qualquer local,** os administradores podem optar por excluir todos os locais **confiáveis** ou **selecionados**.

Por exemplo, algumas organizações podem optar por não exigir autenticação multifatorial quando seus usuários estão conectados à rede em um local confiável, como sua sede física. Os administradores podem criar uma política que inclua qualquer local, mas exclua os locais selecionados para suas redes de sede.

Mais informações sobre os locais podem ser encontradas no artigo, [Qual é a condição de localização no Azure Active Directory Conditional Access](location-condition.md).

## <a name="client-apps-preview"></a>Aplicativos clientes (visualização)

As políticas de acesso condicional por padrão aplicam-se a aplicativos e aplicativos baseados em navegador que utilizam protocolos de autenticação modernos. Além desses aplicativos, os administradores podem optar por incluir clientes Do Exchange ActiveSync e outros clientes que utilizam protocolos legados.

- Navegador
   - Estes incluem aplicativos baseados na Web que usam protocolos como SAML, WS-Federation, OpenID Connect ou serviços registrados como um cliente confidencial OAuth.
- Aplicativos móveis e clientes de desktop
   - Clientes de autenticação modernos
      - Esta opção inclui aplicativos como o desktop office e aplicativos telefônicos.
   - Clientes do Exchange ActiveSync
      - Por padrão, isso inclui todo o uso do protocolo Exchange ActiveSync (EAS). A escolha **da política Aplicar apenas para plataformas suportadas** limitará a plataformas suportadas como iOS, Android e Windows.
      - Quando a diretiva bloqueia o uso do Exchange ActiveSync, o usuário afetado receberá um único e-mail de quarentena. Este e-mail fornece informações sobre por que eles estão bloqueados e incluem instruções de remediação, se possível.
   - Outros clientes
      - Essa opção inclui clientes que usam protocolos básicos/legados de autenticação que não suportam autenticação moderna.
         - SMTP autenticado - Usado por clientes POP e IMAP para enviar mensagens de e-mail.
         - Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e conectar-se às caixas de correio no Exchange Online.
         - Exchange Online PowerShell - Usado para se conectar ao Exchange Online com powershell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, você precisará usar o Módulo PowerShell Online do Exchange para se conectar. Para obter instruções, consulte [Conecte-se ao Exchange Online PowerShell usando autenticação multifatorial](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) - Uma interface de programação usada pelo Outlook, Outlook para Mac e aplicativos de terceiros.
         - IMAP4 - Usado por clientes de e-mail do IMAP.
         - MAPI sobre HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e posterior.
         - Livro de endereços offline (OAB) - Uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.
         - Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e anterior.
         - Serviço Outlook - Usado pelo aplicativo Mail and Calendar para Windows 10.
         - POP3 - Usado por clientes de e-mail POP.
         - Relatórios de Serviços Web - Usado para recuperar dados de relatórios no Exchange Online.

Essas condições são comumente usadas ao exigir um dispositivo gerenciado, bloquear a autenticação legada e bloquear aplicativos da Web, mas permitindo aplicativos móveis ou desktop.

### <a name="supported-browsers"></a>Navegadores com suporte

Essa configuração funciona com todos os navegadores. No entanto, para satisfazer uma política de dispositivo, como um requisito de conformidade de dispositivo, há suporte para os sistemas operacionais e navegadores a seguir:

| Sistema operacional | Navegadores |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Navegador Gerenciado intune, Safari |
| Android | Microsoft Edge, Navegador Gerenciado Intune, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que eu vejo um aviso de certificado no navegador

No Windows 7, iOS, Android e macOS Azure AD identifica o dispositivo usando um certificado cliente que é provisionado quando o dispositivo está registrado no Azure AD.  Quando um usuário faz o primeiro sinal através do navegador, o usuário é solicitado a selecionar o certificado. O usuário deve selecionar este certificado antes de usar o navegador.

#### <a name="chrome-support"></a>Suporte ao Chrome

Para suporte ao Chrome no **Windows 10 Creators Update (versão 1703)** ou posterior, instale a [extensão contas](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)do Windows 10 . Essa extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

Para implantar automaticamente essa extensão para os navegadores Chrome, crie a seguinte chave do registro:

|    |    |
| --- | --- |
| Caminho | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Nome | 1 |
| Type | REG_SZ (String) |
| Dados | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

Para obter suporte ao Chrome no **Windows 8.1 e 7**, crie a seguinte chave do registro:

|    |    |
| --- | --- |
| Caminho | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Nome | 1 |
| Type | REG_SZ (String) |
| Dados | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Esses navegadores dão suporte à autenticação de dispositivo, permitindo que o dispositivo seja identificado e validado em relação a uma política. A verificação de dispositivo falha caso o navegador esteja sendo executado em modo privado.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e cliente de área de trabalho com suporte

As organizações podem selecionar **aplicativos móveis e clientes de desktop** como aplicativo cliente.

Essa configuração tem um impacto nas tentativas de acesso feitas a partir dos seguintes aplicativos móveis e clientes de desktop:

| Aplicativos cliente | Serviço de Destino | Plataforma |
| --- | --- | --- |
| Aplicativo Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| Aplicativo de Calendário/Email/Pessoas, Outlook 2016 Outlook 2013 (com autenticação moderna)| Office 365 Exchange Online | Windows 10 |
| Política de localização e MFA para aplicativos. Políticas baseadas em dispositivos não têm suporte.| Qualquer serviço de aplicativo de Meus Aplicativos | Android e iOS |
| Microsoft Teams Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente – Windows Desktop, iOS, Android, WP e cliente da Web | Equipes da Microsoft | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), [cliente de sincronização OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplicativos do Office 2016, aplicativos do Universal Office, Office 2013 (com autenticação moderna), [cliente de sincronização OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (somente Word, Excel, PowerPoint, OneNote). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Aplicativos móveis do Office | Office 365 SharePoint Online | Android, iOS |
| Aplicativo Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office para macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplicativo móvel do Outlook | Office 365 Exchange Online | Android, iOS |
| Aplicativo do Power BI | Serviço do Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype for Business | Office 365 Exchange Online| Android, iOS |
| Aplicativo Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

### <a name="exchange-activesync-clients"></a>Clientes do Exchange ActiveSync

- As organizações só podem selecionar clientes Do Exchange ActiveSync ao atribuir política a usuários ou grupos. Selecionar **Todos os usuários**, **Todas as funções de hóspede e externo**ou **diretório** farão com que todos os usuários sejam bloqueados.
- Ao criar uma política atribuída aos clientes do Exchange ActiveSync, **o Office 365 Exchange Online** deve ser o único aplicativo em nuvem atribuído à política. 
- As organizações podem limitar o escopo desta política a plataformas específicas usando a condição **de plataformas de dispositivos.**

Se o controle de acesso atribuído à diretiva **usar Exigir aplicativo cliente aprovado,** o usuário é orientado a instalar e usar o cliente móvel do Outlook. No caso de a **autenticação multifatorial** ser necessária, os usuários afetados são bloqueados, porque a autenticação básica não suporta autenticação multifatorial.

Para obter mais informações, consulte os seguintes artigos:

- [Bloqueie a autenticação do legado com o Acesso Condicional](block-legacy-authentication.md)
- [Exigindo aplicativos de clientes aprovados com acesso condicional](app-based-conditional-access.md)

### <a name="other-clients"></a>Outros clientes

Ao selecionar **Outros clientes**, é possível especificar uma condição que afeta os aplicativos que usam autenticação básica com protocolos de email, como IMAP, MAPI, POP, SMTP e aplicativos mais antigos do Office que não usam autenticação moderna.

## <a name="device-state-preview"></a>Estado do dispositivo (visualização)

A condição de estado do dispositivo pode ser usada para excluir dispositivos que são aderidos a Azure AD híbridos e/ou dispositivos marcados como compatíveis com uma política de conformidade microsoft intune a partir das políticas de acesso condicional de uma organização.

Por exemplo, *todos os usuários* que acessam o aplicativo em nuvem microsoft *azure Management,* incluindo todo o **estado do dispositivo,** excluindo **o Dispositivo Híbrido Azure AD, aderiram** e **o dispositivo marcado como compatível** e para controles *de acesso,* **Block**. 
   - Este exemplo criaria uma política que só permite o acesso ao Microsoft Azure Management a partir de dispositivos que são híbridos Azure AD unidos e/ou dispositivos marcados como compatíveis.

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: Subvenção](concept-conditional-access-grant.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
