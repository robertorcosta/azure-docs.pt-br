---
title: Controles de sessão na política de acesso condicional-Azure Active Directory
description: O que são controles de sessão em uma política de acesso condicional do Azure AD
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
ms.openlocfilehash: 84c794e6fe751bc1c12b90353ef7b285f31a2331
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192136"
---
# <a name="conditional-access-session"></a>Acesso condicional: sessão

Em uma política de acesso condicional, um administrador pode fazer uso de controles de sessão para habilitar experiências limitadas em aplicativos de nuvem específicos.

![Política de acesso condicional com um controle Grant que exige autenticação multifator](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrições impostas do aplicativo

As organizações podem usar esse controle para exigir que o Azure AD passe informações sobre o dispositivo para os aplicativos de nuvem selecionados. As informações do dispositivo permitem que os aplicativos na nuvem saibam se uma conexão é iniciada a partir de um dispositivo compatível ou associado a um domínio. Esse controle suporta apenas o SharePoint Online e o Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo na nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações sobre o uso e a configuração de restrições impostas do aplicativo, consulte os seguintes artigos:

- [Ativando acesso limitado com o SharePoint Online](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices)
- [Ativando acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controle de aplicativo de acesso condicional

O Controle de Aplicativos de Acesso Condicional usa uma arquitetura de proxy reverso e é exclusivamente integrado ao acesso condicional do Azure AD. O acesso condicional do Azure AD permite que você aplique controles de acesso nos aplicativos da sua organização com base em determinadas condições. As condições definem quem (usuário ou grupo de usuários) e o que (quais aplicativos de nuvem) e onde (quais locais e redes) uma política de acesso condicional é aplicada. Depois de determinar as condições, você pode encaminhar os usuários para [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) em que você pode proteger dados com controle de aplicativos de acesso condicional aplicando controles de acesso e sessão.

Controle de Aplicativos de Acesso Condicional permite que o acesso de aplicativo do usuário e as sessões sejam monitoradas e controladas em tempo real com base nas políticas de acesso e sessão. As políticas de acesso e sessão são usadas no portal de Cloud App Security para refinar ainda mais os filtros e definir ações a serem executadas em um usuário. Com as políticas de acesso e sessão, você pode:

- Impedir vazamento de dados: você pode bloquear o download, recortar, copiar e imprimir documentos confidenciais em, por exemplo, dispositivos não gerenciados.
- Proteger no download: em vez de bloquear o download de documentos confidenciais, você pode exigir que os documentos sejam rotulados e protegidos com a proteção de informações do Azure. Essa ação garante que o documento esteja protegido e o acesso do usuário seja restrito em uma sessão potencialmente arriscada.
- Impedir o carregamento de arquivos sem rótulo: antes de um arquivo confidencial ser carregado, distribuído e usado por outros, é importante certificar-se de que o arquivo tem o rótulo e a proteção corretos. Você pode garantir que arquivos sem rótulo com conteúdo confidencial sejam impedidos de serem carregados até que o usuário classificar o conteúdo.
- Monitorar sessões de usuário para fins de conformidade: os usuários arriscados são monitorados quando entram em aplicativos e suas ações são registradas de dentro da sessão. Você pode investigar e analisar o comportamento do usuário para entender onde e em quais condições as políticas de sessão devem ser aplicadas no futuro.
- Bloquear o acesso: você pode bloquear o acesso de maneira granular para aplicativos e usuários específicos, dependendo de vários fatores de risco. Por exemplo, você pode bloqueá-los se eles estiverem usando certificados de cliente como uma forma de gerenciamento de dispositivo.
- Bloquear atividades personalizadas: alguns aplicativos têm cenários exclusivos que trazem risco, por exemplo, enviar mensagens com conteúdo confidencial em aplicativos como equipes da Microsoft ou margem de atraso. Nesses tipos de cenários, você pode verificar mensagens quanto ao conteúdo confidencial e bloqueá-los em tempo real.

Para obter mais informações, consulte o artigo [implantar controle de aplicativos de acesso condicional para aplicativos em destaque](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Frequência de entrada (versão prévia)

A frequência de entrada define o período de tempo antes que um usuário seja solicitado a entrar novamente ao tentar acessar um recurso.

A configuração frequência de entrada funciona com aplicativos que implementaram protocolos OAUTH2 ou OIDC de acordo com os padrões. A maioria dos aplicativos nativos da Microsoft para Windows, Mac e Mobile, incluindo os seguintes aplicativos Web, estão em conformidade com a configuração.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Portal de administração do O365
- Exchange Online
- SharePoint e OneDrive
- Cliente Web de equipes
- Dynamics CRM Online
- Portal do Azure

Para obter mais informações, consulte o artigo [Configurar o gerenciamento de sessão de autenticação com acesso condicional](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Sessão persistente do navegador (versão prévia)

Uma sessão persistente do navegador permite que os usuários permaneçam conectados após fechar e reabrir a janela do navegador.

Para obter mais informações, consulte o artigo [Configurar o gerenciamento de sessão de autenticação com acesso condicional](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Próximas etapas

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo somente de relatório](concept-conditional-access-report-only.md)
