---
title: Crie resiliência em sua infraestrutura IAM com Azure Active Directory
description: Um guia para arquitetos e administradores de ti que não estão criando resiliência para a interrupção de sua infraestrutura IAM.
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
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919275"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Crie resiliência em sua infraestrutura de gerenciamento de identidade e acesso

Azure Active Directory é um sistema de gerenciamento de acesso e identidade de nuvem global que fornece serviços críticos como autenticação e autorização para os recursos da sua organização. Este documento fornece orientação para entender, conter e atenuar o risco de interrupção dos serviços de autenticação ou autorização para recursos que dependem do Azure Active Directory (AD do Azure). 

O conjunto de documentos foi projetado para

* Arquitetos de identidade

* Proprietários do serviço de identidade

* Equipes de operações de identidade

Consulte também a documentação para [desenvolvedores de aplicativos](https://aka.ms/azureadresilience/developer) e para [sistemas Azure ad B2C](resilience-b2c.md).

## <a name="what-is-resilience"></a>O que é resiliência?

No contexto da sua infraestrutura de identidade, a resiliência é a capacidade de suportar a interrupção em serviços como autenticação e autorização, ou falha de outros componentes, com impacto mínimo ou não para seus negócios, usuários e operações. O impacto da interrupção pode ser grave, e a resiliência exige planejamento.

## <a name="why-worry-about-disruption"></a>Por que se preocupar com interrupções?

Todas as chamadas para o sistema de autenticação estão sujeitas à interrupção se algum componente na cadeia da chamada para o Azure AD falhar. Isso significa que se qualquer parte da sua infraestrutura tiver um problema, o trabalho pode ser interrompido porque os usuários não podem acessar os aplicativos de que precisam. Portanto, a redução do número de chamadas de autenticação e o número de dependências nessas chamadas é importante para sua resiliência. Os desenvolvedores de aplicativos podem declarar algum controle sobre a frequência com que os tokens são solicitados. Por exemplo, trabalhe com seus desenvolvedores para garantir que eles estejam usando identidades gerenciadas do Azure AD para seus aplicativos sempre que possível. 

Em um sistema de autenticação baseado em token como o Azure AD, o aplicativo (cliente) do usuário deve adquirir um token de segurança do sistema de identidade antes de poder acessar um aplicativo ou outro recurso. Durante o período de validade, um cliente pode apresentar o mesmo token várias vezes para acessar o aplicativo.

Quando o token apresentado ao aplicativo expira, o aplicativo rejeita o token e o cliente deve adquirir um novo token do Azure AD. A aquisição de um novo token pode exigir interação do usuário, como prompts de credenciais. Reduzir a frequência de chamadas de autenticação com tokens de vida maior reduz esse risco. No entanto, você deve balancear a vida útil do token com o risco criado por menos avaliações de política. Para obter mais informações sobre o gerenciamento de tempos de vida de token, consulte este artigo sobre como [otimizar prompts de reautenticação](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

## <a name="ways-to-increase-resilience"></a>Maneiras de aumentar a resiliência
O diagrama a seguir mostra seis maneiras concretas de aumentar a resiliência. Cada método é explicado em detalhes nos artigos vinculados na parte próximas etapas deste artigo.
  
![Diagrama mostrando a visão geral da resiliência do administrador](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)
