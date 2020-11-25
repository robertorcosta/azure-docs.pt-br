---
title: Crie resiliência usando a avaliação de acesso contínuo no Azure Active Directory
description: Um guia para arquitetos e administradores de ti sobre como usar o CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919271"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Criar resiliência usando a avaliação de acesso contínuo

A [Continuous Access Evaluation](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) (CAE) permite que os aplicativos do Azure ad assinem eventos críticos que podem ser avaliados e aplicados. Isso inclui a avaliação dos seguintes eventos:

* A conta de usuário que está sendo excluída ou desabilitada

* A senha de um usuário foi alterada

* A MFA está habilitada para o usuário.

* O administrador revoga explicitamente um token.

* O risco de usuário elevado é detectado.

Como resultado, os aplicativos podem rejeitar tokens não expirados com base nos eventos sinalizados pelo Azure AD, conforme descrito no diagrama a seguir.

![conceptualiagram de CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Como o CAE ajuda?

Esse mecanismo permite que o Azure AD emita tokens de vida útil, permitindo, ao mesmo tempo, aplicativos uma maneira de revogar o acesso e forçar a nova autenticação quando necessário. O resultado líquido desse padrão é menos chamadas para adquirir tokens, o que significa que o fluxo de ponta a ponta é mais resiliente. 

Para usar o CAE, o serviço e o cliente devem ter capacidade de CAE. Microsoft 365 serviços como o Exchange Online, as equipes e o SharePoint Online dão suporte ao CAE. No lado do cliente, experiências baseadas em navegador que usam esses serviços do Office 365 (por exemplo, o Outlook Web App) e versões específicas de clientes nativos do Office 365 são compatíveis com a CAE. Mais serviços em nuvem da Microsoft se tornarão com capacidade de CAE.

A Microsoft está trabalhando com o setor para criar [padrões](https://openid.net/wg/sse/) que permitirão que aplicativos de terceiros usem esse recurso. Você também pode desenvolver aplicativos com capacidade de CAE. Consulte como criar resiliência em seu aplicativo para obter mais informações.

## <a name="how-do-i-implement-cae"></a>Como fazer implementar o CAE?

* [Habilite o CAE](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) na configuração de segurança do Azure AD.

* Verifique se sua organização está usando [versões compatíveis](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) do Microsoft Office aplicativos nativos.

* [Otimize seus prompts de reautenticação](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

 
## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)
