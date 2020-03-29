---
title: Habilitar o SSO para seu aplicativo multilocatário
description: Orientação para fornecedores independentes de software sobre a integração com o Diretório ativo do Azure
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795173"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Habilite o login único para o seu aplicativo de vários inquilinos  

Quando você oferece seu aplicativo de uso por outras empresas através de uma compra ou assinatura, você disponibiliza seu aplicativo para os clientes dentro de seus próprios inquilinos do Azure. Isso é conhecido como criação de um aplicativo de vários inquilinos. Para obter uma visão geral deste conceito, consulte [Aplicativos Multilocatários no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [Tenancy no Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>O que é o único signo

O sode-on único (SSO) adiciona segurança e conveniência quando os usuários acessam aplicativos usando o Azure Active Directory e outras identidades. Quando um aplicativo é habilitado para SSO, os usuários não têm que inserir credenciais separadas para acessar esse aplicativo. Para uma explicação completa do single sign-on. [Consulte O login único em aplicativos no Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Por que ativar o login único em sua aplicação?

Existem muitas vantagens em habilitar o SSO em seu aplicativo multi-inquilino. Quando você habilita o SSO para sua aplicação:

* Seu aplicativo pode ser listado no Azure Marketplace, onde seu aplicativo é descoberto por milhões de organizações que usam o Azure Active Directory.
  * Permite que os clientes configurem rapidamente o aplicativo com o Azure AD.

* Seu aplicativo pode ser descoberto na Galeria de Aplicativos office 365, no Office 365 App Launcher e no Microsoft Search on Office.com

* Seu aplicativo pode usar a API Microsoft Graph REST para acessar os dados que impulsionam a produtividade do usuário que está disponível no Microsoft Graph.

* Você reduz os custos de suporte, facilitando para seus clientes.
  * A documentação específica do aplicativo coproduzida com a equipe do Azure AD para nossos clientes mútuos facilita a adoção.
  * Se o SSO de um clique estiver ativado, os administradores de TI de seus clientes não terão que aprender a configurar seu aplicativo para uso em sua organização.

* Você fornece aos seus clientes a capacidade de gerenciar completamente a autenticação e a autorização de seus funcionários e clientes.

  * Colocando toda a responsabilidade de gerenciamento de contas e conformidade com o cliente proprietário dessas identidades.

  * Fornecendo capacidade de ativar ou desativar o SSO para provedores, grupos ou usuários de identidade específicos para atender às suas necessidades de negócios.

* Você aumenta sua comercialização e adoção. Muitas grandes organizações exigem que (ou aspiram) que seus funcionários tenham experiências perfeitas de SSO em todos os aplicativos. Facilitar o SSO é importante.

* Você reduz o atrito do usuário final, o que pode aumentar o uso do usuário final e aumentar sua receita.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como ativar o Single Sign-on em seu aplicativo publicado

1. [Escolha o protocolo de federação certo para sua aplicação multi-inquilino](isv-choose-multi-tenant-federation.md).
1. Implementar SSO em seu aplicativo
   - Veja [orientações sobre padrões de autenticação](../develop/v2-app-types.md)
   - Consulte [amostras de código de diretório ativo do Azure](../develop/sample-v2-code.md) para protocolos OIDC e OAuth
1. [Crie seu Inquilino Azure](isv-tenant-multi-tenant-app.md) e teste sua aplicação
1. [Crie e publique a documentação SSO em seu site.](isv-create-sso-documentation.md)
1. [Envie sua lista de aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e faça parceria com a Microsoft para criar documentação no site da Microsoft.
1. [Junte-se à Microsoft Partner Network (gratuitamente) e crie seu plano de ir para o mercado](https://partner.microsoft.com/en-us/explore/commercial#gtm).
