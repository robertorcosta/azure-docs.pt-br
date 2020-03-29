---
title: Controles de sessão na política de Acesso Condicional - Diretório Ativo do Azure
description: Quais são os controles de sessão em uma política de acesso condicional do Azure AD
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671898"
---
# <a name="conditional-access-session"></a>Acesso Condicional: Sessão

Dentro de uma política de Acesso Condicional, um administrador pode fazer uso de controles de sessão para permitir experiências limitadas em aplicativos específicos na nuvem.

![Política de Acesso Condicional com controle de subvenção que exija autenticação multifatorial](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrições impostas pelo aplicativo

As organizações podem usar esse controle para exigir que o Azure AD passe informações do dispositivo para os aplicativos selecionados na nuvem. As informações do dispositivo permitem que os aplicativos na nuvem saibam se uma conexão é iniciada a partir de um dispositivo compatível ou associado a um domínio. Esse controle suporta apenas o SharePoint Online e o Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo na nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações sobre o uso e configuração das restrições impostas pelo aplicativo, consulte os seguintes artigos:

- [Ativando acesso limitado com o SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Ativando acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controle de aplicativos de acesso condicional

O Conditional Access App Control usa uma arquitetura proxy reversa e é exclusivamente integrado ao Azure AD Conditional Access. O acesso condicional do Azure AD permite que você imponha controles de acesso aos aplicativos da sua organização de acordo com determinadas condições. As condições definem a quem (usuário ou grupo de usuários), ao que (quais aplicativos de nuvem) e a que local (quais localizações e redes) uma política de acesso condicional é aplicável. Depois de determinar as condições, você pode direcionar os usuários para o [Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) onde você pode proteger dados com controle de aplicativo de acesso condicional, aplicando controles de acesso e sessão.

O Controle de Aplicativo de Acesso Condicional permite o monitoramento e controle em tempo real do acesso e das sessões do aplicativo com base nas políticas de acesso e de sessão. As políticas de acesso e de sessão são utilizadas dentro do portal do Cloud App Security para refinar ainda mais os filtros e definir as ações a serem executadas em relação a um usuário. Com as políticas de acesso e de sessão, é possível:

- Evitar a exfiltração de dados: Você pode bloquear o download, o corte, a cópia e a impressão de documentos confidenciais em, por exemplo, dispositivos não gerenciados.
- Proteger no download: Em vez de bloquear o download de documentos confidenciais, você pode exigir que os documentos sejam rotulados e protegidos com o Azure Information Protection. Essa ação garante que o documento seja protegido e o acesso do usuário seja restrito em uma sessão potencialmente arriscada.
- Evite o upload de arquivos não rotulados: Antes que um arquivo sensível seja carregado, distribuído e usado por outros, é importante certificar-se de que o arquivo tem o rótulo e proteção certos. Você pode garantir que arquivos não rotulados com conteúdo sensível sejam impedidos de serem carregados até que o usuário classifique o conteúdo.
- Monitore as sessões dos usuários para conformidade: os usuários de risco são monitorados quando entram em aplicativos e suas ações são registradas dentro da sessão. É possível investigar e analisar o comportamento do usuário para compreender onde e sob quais condições as políticas de sessão deverão ser aplicadas no futuro.
- Bloquear o acesso: Você pode bloquear granularmente o acesso para aplicativos e usuários específicos, dependendo de vários fatores de risco. Por exemplo, você pode bloqueá-los se eles estiverem usando certificados de cliente como uma forma de gerenciamento de dispositivos.
- Bloqueie atividades personalizadas: Alguns aplicativos têm cenários exclusivos que carregam riscos, por exemplo, enviando mensagens com conteúdo sensível em aplicativos como Microsoft Teams ou Slack. Nesses tipos de cenários, você pode escanear mensagens para conteúdo sensível e bloqueá-las em tempo real.

Para obter mais informações, consulte o artigo [Deploy Conditional Access App Control para aplicativos em destaque](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Freqüência de login (Visualização)

A freqüência de login define o período de tempo antes que um usuário seja solicitado a fazer login novamente ao tentar acessar um recurso.

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

Para obter mais informações, consulte o artigo Configure o [gerenciamento da sessão de autenticação com o Conditional Access](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Sessão de navegador persistente (Visualização)

Uma sessão persistente do navegador permite que os usuários permaneçam conectado após o fechamento e a reabertura da janela do navegador.

Para obter mais informações, consulte o artigo Configure o [gerenciamento da sessão de autenticação com o Conditional Access](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Próximas etapas

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

- [Modo somente relatório](concept-conditional-access-report-only.md)
