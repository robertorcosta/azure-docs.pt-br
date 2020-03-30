---
title: Configurar o gerenciamento da sessão de autenticação - Azure Active Directory
description: Personalize a configuração da sessão de autenticação Azure AD, incluindo o sinal do usuário na freqüência e a persistência da sessão do navegador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263277"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurar o gerenciamento da sessão de autenticação com o Conditional Access

Em implantações complexas, as organizações podem ter a necessidade de restringir as sessões de autenticação. Alguns cenários podem incluir:

* Acesso a recursos de um dispositivo não gerenciado ou compartilhado
* Acesso a informações confidenciais de uma rede externa
* Usuários de alto impacto
* Aplicativos de negócios críticos

Os controles de acesso condicional permitem criar políticas que visam casos de uso específicos dentro de sua organização sem afetar todos os usuários.

Antes de mergulhar em detalhes sobre como configurar a diretiva, vamos examinar a configuração padrão.

## <a name="user-sign-in-frequency"></a>Frequência de login do usuário

A freqüência de login define o período de tempo antes que um usuário seja solicitado a fazer login novamente ao tentar acessar um recurso.

A configuração padrão do Azure Active Directory (Azure AD) para o sinal do usuário na frequência é uma janela de rolagem de 90 dias. Pedir credenciais aos usuários muitas vezes parece uma coisa sensata a se fazer, mas pode sair pela culatra: os usuários que são treinados para inserir suas credenciais sem pensar podem fornecê-los involuntariamente a um prompt de credencial malicioso.

Pode parecer alarmante não pedir que um usuário faça login de volta, na realidade qualquer violação das políticas de TI revogará a sessão. Alguns exemplos incluem (mas não se limitam a) uma alteração de senha, um dispositivo incompatível ou desabilitação da conta. Você também pode revogar explicitamente [as sessões dos usuários usando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). A configuração padrão do Azure AD se resume a "não peça aos usuários que forneçam suas credenciais se a postura de segurança de suas sessões não tiver sido alterada".

A configuração de freqüência de login funciona com aplicativos que implementaram protocolos OAUTH2 ou OIDC de acordo com as normas. A maioria dos aplicativos nativos da Microsoft para Windows, Mac e Mobile, incluindo os seguintes aplicativos da Web, está em conformidade com a configuração.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal O365 Admin
- Exchange Online
- SharePoint e OneDrive
- Equipes web cliente
- Dynamics CRM Online
- Portal do Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Freqüência de login do usuário e identidades do dispositivo

Se você tiver o Azure AD unido, o Azure AD híbrido se juntou ou dispositivos registrados no Azure AD, quando um usuário desbloquear seu dispositivo ou sinais de forma interativa, este evento também satisfará a política de sinal na freqüência. Nos 2 exemplos a freqüência de login do usuário a seguir é definida como 1 hora:

Exemplo 1:

- Às 00:00, um usuário entra no seu dispositivo Azure AD do Windows 10 e começa a trabalhar em um documento armazenado no SharePoint Online.
- O usuário continua trabalhando no mesmo documento em seu dispositivo por uma hora.
- Às 01:00, o usuário é solicitado a fazer login novamente com base no requisito de freqüência de login na política de Acesso Condicional configurada por seu administrador.

Exemplo 2:

- Às 00:00, um usuário entra no seu dispositivo Azure AD do Windows 10 e começa a trabalhar em um documento armazenado no SharePoint Online.
- Às 00:30, o usuário se levanta e faz uma pausa bloqueando seu dispositivo.
- Às 00:45, o usuário retorna de sua pausa e desbloqueia o dispositivo.
- Às 01:45, o usuário é solicitado a fazer login novamente com base no requisito de freqüência de login na política de Acesso Condicional configurada por seu administrador desde que o último login aconteceu às 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão persistente do navegador permite que os usuários permaneçam conectado após o fechamento e a reabertura da janela do navegador.

O padrão Azure AD para persistência de sessão de navegador permite que os usuários em dispositivos pessoais escolham se devem persistir a sessão mostrando um "Stay signed in?" prompt após autenticação bem sucedida. Se a persistência do navegador estiver configurada no AD FS usando a orientação no artigo [AD FS Single Sign-On Settings](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), cumpriremos essa política e persistiremos na sessão do Azure AD também. Você também pode configurar se os usuários do seu inquilino vero "Stay signed in?" solicitar alterando a configuração apropriada no painel de marca da empresa no portal Azure usando a orientação no artigo [Personalizar sua página de login Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configuração de controles de sessão de autenticação

O Conditional Access é um recurso Azure AD Premium e requer uma licença premium. Se você gostaria de saber mais sobre o Conditional Access, veja [o que é acesso condicional no Diretório Ativo do Azure?](overview.md#license-requirements)

> [!WARNING]
> Se você estiver usando o recurso [configurável token lifetime](../develop/active-directory-configurable-token-lifetimes.md) atualmente em visualização pública, observe que não suportamos criar duas políticas diferentes para a mesma combinação de usuário ou aplicativo: uma com esse recurso e outra com recurso de vida útil de token configurável. A Microsoft planeja aposentar o recurso de vida útil do token configurável em 1º de maio de 2020 e substituí-lo pelo recurso de gerenciamento de sessão de autenticação de acesso condicional.  

### <a name="policy-1-sign-in-frequency-control"></a>Política 1: Controle de freqüência de login

1. Criar nova política
1. Escolha todas as condições necessárias para o ambiente do cliente, incluindo os aplicativos de nuvem alvo.

   > [!NOTE]
   > Recomenda-se definir a freqüência de solicitação de autenticação igual para os principais aplicativos do Microsoft Office, como o Exchange Online e o SharePoint Online, para obter a melhor experiência do usuário.

1. Vá para a**sessão** **controles de** > acesso e clique **na freqüência de login**
1. Digite o valor necessário de dias e horas na primeira caixa de texto
1. Selecione um valor de **Horas** ou **Dias** a partir da queda
1. Salve sua apólice

![Política de Acesso Condicional configurada para sinal na frequência](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD, o login registrado do Windows no dispositivo é considerado um alerta. Por exemplo, se você configurou o Sinal em frequência para 24 horas para aplicativos do Office, os usuários em dispositivos Windows registrados no Azure AD satisfarão a política de registro de freqüência ao fazer login no dispositivo e não serão solicitados novamente ao abrir aplicativos do Office.

Se você tiver configurado uma freqüência de login diferente para diferentes aplicativos da Web que estão sendo executados na mesma sessão do navegador, a política mais rigorosa será aplicada a ambos os aplicativos porque todos os aplicativos em execução na mesma sessão do navegador compartilham um único token de sessão.

### <a name="policy-2-persistent-browser-session"></a>Política 2: Sessão persistente do navegador

1. Criar nova política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Observe que este controle requer escolher "Todos os Aplicativos em Nuvem" como uma condição. A persistência da sessão do navegador é controlada pelo token de sessão de autenticação. Todas as guias em uma sessão do navegador compartilham um único token de sessão e, portanto, todas devem compartilhar o estado de persistência.

1. Vá para a**sessão** **controles de** > acesso e clique **na sessão persistente do navegador**
1. Selecione um valor a partir da queda
1. Salvar-lhe a política

![Política de acesso condicional configurada para navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> A configuração de sessão de navegador persistente no Azure AD Conditional Access substituirá a "Stay signed in?" configuração no painel de marca da empresa no portal Azure para o mesmo usuário se você tiver configurado ambas as políticas.

## <a name="validation"></a>Validação

Use a ferramenta E-Se para simular um login do usuário para o aplicativo de destino e outras condições com base em como você configurou sua política. Os controles de gerenciamento da sessão de autenticação aparecem no resultado da ferramenta.

![Acesso Condicional E se os resultados da ferramenta](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implantação de política

Para garantir que sua política funcione conforme o esperado, a melhor prática é testá-la antes de distribuí-la em produção. O ideal é usar um locatário de teste para verificar se a nova política funciona conforme o esperado. Para obter mais informações, consulte o artigo [Melhores práticas para acesso condicional no Diretório Ativo do Azure](best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* Se você quiser saber como configurar uma política de acesso condicional, consulte o artigo [Exigir MFA para aplicativos específicos com o Azure Active Directory Conditional Access](app-based-mfa.md).
* Se você estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o artigo [Práticas recomendadas para acesso condicional no Diretório Ativo do Azure](best-practices.md).
