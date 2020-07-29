---
title: Habilitar o SSO para seu aplicativo multilocatário
description: Diretrizes para fornecedores independentes de software na integração com o Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cabcaacbdc7e3e94e62c8fb45274c4d4f9390ddd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273398"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Habilitar o logon único para seu aplicativo multilocatário  

Quando você oferece seu aplicativo para uso por outras empresas por meio de uma compra ou assinatura, torna seu aplicativo disponível para clientes em seus próprios locatários do Azure. Isso é conhecido como criar um aplicativo multilocatário. Para obter uma visão geral desse conceito, consulte [aplicativos multilocatário no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [locação em Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>O que é logon único

O SSO (logon único) adiciona segurança e conveniência quando os usuários entram em aplicativos usando Azure Active Directory e outras identidades. Quando um aplicativo é habilitado para SSO, os usuários não precisam inserir credenciais separadas para acessar esse aplicativo. Para obter uma explicação completa do logon único. [Consulte logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Por que habilitar o logon único em seu aplicativo?

Há muitas vantagens em habilitar o SSO em seu aplicativo multilocatário. Quando você habilita o SSO para seu aplicativo:

* Seu aplicativo pode ser listado no Azure Marketplace, onde seu aplicativo é detectável por milhões de organizações usando Azure Active Directory.
  * Permite que os clientes configurem rapidamente o aplicativo com o Azure AD.

* Seu aplicativo pode ser detectável na Galeria de aplicativos do Office 365, no iniciador de aplicativos do Office 365 e no Microsoft Search no Office.com

* Seu aplicativo pode usar a API REST Microsoft Graph para acessar os dados que impulsionam a produtividade do usuário que está disponível no Microsoft Graph.

* Você reduz os custos de suporte ao facilitar para seus clientes.
  * A documentação específica do aplicativo coproduzida com a equipe do Azure AD para nossos clientes mútuos facilita a adoção.
  * Se o SSO de um clique estiver habilitado, os administradores de ti de seus clientes não precisarão saber como configurar seu aplicativo para uso em sua organização.

* Você fornece aos seus clientes a capacidade de gerenciar completamente sua autenticação e autorização de funcionários e de identidades de convidado.

  * Colocar todas as responsabilidades de conformidade e gerenciamento de conta com o proprietário do cliente dessas identidades.

  * Fornecendo a capacidade de habilitar ou desabilitar o SSO para provedores de identidade, grupos ou usuários específicos para atender às suas necessidades comerciais.

* Você aumenta seu mercado e sua adoção. Muitas organizações de grande porte exigem que (ou almeje) seus funcionários tenham experiências de SSO contínuos em todos os aplicativos. Tornar o SSO fácil é importante.

* Você reduz o conflito do usuário final, o que pode aumentar o uso do usuário final e aumentar sua receita.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como habilitar o logon único em seu aplicativo publicado

1. [Escolha o protocolo de Federação correto para seu aplicativo multilocatário](isv-choose-multi-tenant-federation.md).
1. Implementar o SSO em seu aplicativo
   - Consulte as [diretrizes sobre padrões de autenticação](../develop/v2-app-types.md)
   - Confira [exemplos de código do Azure Active Directory](../develop/sample-v2-code.md) para protocolos OIDC e OAuth
1. [Criar seu locatário do Azure](isv-tenant-multi-tenant-app.md) e testar seu aplicativo
1. [Crie e publique a documentação do SSO no seu site](isv-create-sso-documentation.md).
1. [Envie sua listagem de aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e parceiro com a Microsoft para criar a documentação no site da Microsoft.
1. [Junte-se ao Microsoft Partner Network (gratuito) e crie seu plano ir para o mercado](https://partner.microsoft.com/explore/commercial#gtm).
