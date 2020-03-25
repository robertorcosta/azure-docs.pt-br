---
title: Portal de inscrição de autoatendimento para a Colaboração B2B – Azure AD
description: A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195786"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Autoatendimento para inscrição na colaboração B2B do Azure AD

Os clientes podem fazer muito com os recursos internos expostos por meio do [Portal do Azure](https://portal.azure.com) e o [Painel de Acesso de Aplicativos](https://myapps.microsoft.com) para usuários finais. Porém, você pode precisar personalizar o fluxo de trabalho de integração para usuários B2B para se adequar às necessidades da sua organização.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Gerenciamento de direitos do Azure AD para inscrição de usuário convidado B2B

Como uma organização que faz o convite, talvez você não saiba com antecedência quem são os colaboradores externos individuais que precisam de acesso aos seus recursos. Você precisa encontrar uma maneira para os usuários de empresas parceiras se inscreverem com as políticas que você controla. Caso deseje permitir que os usuários de outras organizações solicitem o acesso e que, após a aprovação, eles sejam provisionados com as contas de convidado e atribuídos a grupos, aplicativos e sites do SharePoint Online, use o [gerenciamento de direitos do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) para configurar políticas que [gerenciam o acesso de usuários externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API de convite do Azure Active Directory B2B

As organizações podem usar a [API do gerenciador de convites do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) para criar as próprias experiências de integração para os usuários convidados B2B. Quando você desejar oferecer a inscrição para autoatendimento de usuário convidado B2B, recomendaremos usar o [gerenciamento de direitos do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Mas se desejar criar a própria experiência, use a [API de criação de convite](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) para enviar de modo automático seu email de convite personalizado diretamente ao usuário B2B, por exemplo. Ou então seu aplicativo pode usar a inviteRedeemUrl retornada na resposta de criação para criar o próprio convite (por meio do mecanismo de comunicação de sua escolha) para o usuário convidado.

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Licenciamento da colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
