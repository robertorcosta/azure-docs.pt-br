---
title: Orientação de licenciamento de colaboração B2B - Azure Active Directory | Microsoft Docs
description: A colaboração do Azure do Azure Active Directory B2B não exige licenças pagas do Azure AD, mas você pode também obter recursos pagos para usuários convidados de B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868840"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Diretrizes de licenciamento da colaboração B2B do Azure Active Directory

Com a colaboração do Azure AD (Azure Active Directory) B2B (entre empresas), você pode convidar usuários externos (ou "usuários convidados") para usar seus serviços pagos do Azure AD. Alguns recursos são gratuitos, mas para qualquer recurso ad pago do Azure, você pode convidar até cinco usuários convidados para cada licença de edição Azure AD que você possui para um funcionário ou um usuário não convidado em seu inquilino.

> [!NOTE]
> Consulte os [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) para obter detalhes sobre os preços do Azure AD e os recursos de colaboração B2B.

O licenciamento do usuário convidado B2B é calculado automaticamente e relatado com base na proporção 1:5. 

Além disso, os usuários convidados podem usar os recursos gratuitos do Azure AD sem requisitos adicionais de licenciamento. Os usuários convidados têm acesso aos recursos gratuitos do Azure AD, mesmo quando você não tem nenhuma licença paga do Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: calculando licenças de usuário convidado
Depois de determinar quantos usuários convidados precisam acessar seus serviços pagos do Azure AD, verifique se você tem licenças pagas do Azure AD suficientes para cobrir os usuários convidados na proporção 1:5 necessária. Estes são alguns exemplos:

- Você deseja convidar 100 usuários convidados para seus aplicativos ou serviços Azure AD e fornecer gerenciamento de acesso e provisionamento. Para 50 desses usuários convidados, você também deseja exigir MFA e Conditional Access, então para esses recursos você precisará de 10 licenças Azure AD Premium P1. Se planejar usar recursos do Identity Protection com os usuários convidados, você precisará de licenças do Azure AD Premium P2 na mesma proporção de 1:5 para cobrir os usuários convidados.
- Você deseja convidar 60 usuários que exigirão MFA, portanto, você precisa ter pelo menos 12 licenças do Azure AD Premium P1. Você tem 10 funcionários com licenças do Azure AD Premium P1, o que permite até 50 usuários convidados na proporção de licenciamento 1:5. Você precisará comprar duas licenças Premium P1 adicionais para cobrir os 10 usuários convidados adicionais.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes recursos sobre a colaboração B2B do Azure AD:

* [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração Azure AD B2B?](what-is-b2b.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
